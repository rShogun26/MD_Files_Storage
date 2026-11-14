## 1. Discord Asset Management & Automation Suite

This toolkit contains browser console scripts and bookmarklets to extract, optimize, manage, and batch-clean Discord emojis and favorite GIFs.

---

### Tool 1: Discord Favorite GIFs to JSON Exporter

Extracts and exports your Discord Favorite GIFs into a clean, standalone JSON file containing only high-resolution `src` URLs. Automatically parses Discord's internal cache / React Fiber / Settings Protobuf, auto-refreshes expiring CDN attachment links via Discord's API, and upgrades compressed `.mp4` previews (Tenor, Giphy, Imgur) to full-quality `.gif` links.

#### How to Use:
1. Open [Discord Web](https://discord.com/app) in your browser and log in.
2. Press `Ctrl + Shift + I` (or `Cmd + Option + I` on macOS) and switch to the **Console** tab.
3. If prompted by Discord, type `allow pasting` and press Enter.
4. Paste the script below and hit Enter. The high-quality `.json` file will download automatically.

#### Script (`discord-fav-gifs-exporter.js`):
<details>
<summary>Click to expand script</summary>

```javascript
/**
 * Discord Favorite GIF High-Quality `src` JSON Exporter (v5 - JSON Only)
 * Automatically detects all Favorite GIFs, refreshes expiring Discord CDN links,
 * upgrades .mp4 links to Full-HD .gif links, and exports ONLY the `src` URLs to JSON.
 */
(async function ExportDiscordFavoriteGifSrcsOnly() {
    console.log("%c[GIF JSON Exporter] Extracting Favorite GIFs...", "color:#5865F2;font-weight:bold;font-size:14px;");

    // =========================================================================
    // 1. EXTRACT WEBPACK/RSPACK MODULE CACHE & DISCORD INTERNALS
    // =========================================================================
    function isGifMap(obj) {
        if (!obj || typeof obj !== "object" || Array.isArray(obj)) return false;
        const keys = Object.keys(obj);
        if (keys.length === 0) return false;
        const first = obj[keys[0]];
        return (
            first &&
            typeof first === "object" &&
            typeof first.src === "string" &&
            (typeof first.width === "number" || typeof first.height === "number") &&
            ("order" in first || "format" in first)
        );
    }

    function getDiscordInternals() {
        let wpRequire = null;
        const chunkGlobal = window.webpackChunkdiscord_app;

        if (Array.isArray(chunkGlobal)) {
            const chunkId = "_dgd_" + Date.now();
            chunkGlobal.push([[chunkId], {}, (req) => { wpRequire = req; }]);
            chunkGlobal.pop();
        }

        let moduleCache = wpRequire?.c || null;
        if (wpRequire && !moduleCache) {
            const probeId = "__dgd_probe_" + Math.random().toString(36).slice(2);
            try {
                Object.defineProperty(Object.prototype, probeId, {
                    configurable: true,
                    get() {
                        delete Object.prototype[probeId];
                        moduleCache = this;
                        return { exports: {} };
                    }
                });
                wpRequire(probeId);
            } catch {} finally {
                delete Object.prototype[probeId];
            }
        }

        let protoStore = null;
        let token = null;
        let restApi = null;
        let foundGifsMap = null;
        const visited = new WeakSet();

        function searchForGifMap(obj, depth = 0) {
            if (!obj || typeof obj !== "object" || depth > 3 || visited.has(obj)) return;
            visited.add(obj);

            if (isGifMap(obj)) {
                if (!foundGifsMap || Object.keys(obj).length > Object.keys(foundGifsMap).length) {
                    foundGifsMap = obj;
                }
                return;
            }
            if (obj.favoriteGifs?.gifs && isGifMap(obj.favoriteGifs.gifs)) {
                foundGifsMap = obj.favoriteGifs.gifs;
                return;
            }
            if (depth < 3) {
                for (const k of Object.keys(obj)) {
                    if (k === "window" || k === "document" || k === "prototype") continue;
                    try {
                        const child = obj[k];
                        if (child && typeof child === "object") searchForGifMap(child, depth + 1);
                    } catch {}
                }
            }
        }

        function inspectCandidate(item) {
            if (!item || (typeof item !== "object" && typeof item !== "function")) return;

            try {
                if ("frecencyWithoutFetchingLatest" in item) {
                    protoStore = item;
                    const gifs = item.frecencyWithoutFetchingLatest?.favoriteGifs?.gifs;
                    if (isGifMap(gifs)) foundGifsMap = gifs;
                }
            } catch {}

            try { searchForGifMap(item, 0); } catch {}

            try {
                if (!token && typeof item.getToken === "function") {
                    const t = item.getToken();
                    if (typeof t === "string" && t.length > 20) token = t;
                }
            } catch {}

            try {
                if (
                    !restApi &&
                    typeof item.get === "function" &&
                    typeof item.post === "function" &&
                    typeof item.put === "function" &&
                    typeof item.patch === "function"
                ) {
                    restApi = item;
                }
            } catch {}
        }

        function inspectModuleExports(exp) {
            if (!exp) return;
            inspectCandidate(exp);
            if (typeof exp === "object" || typeof exp === "function") {
                try {
                    for (const key of Reflect.ownKeys(exp)) {
                        if (key === "length" || key === "prototype" || key === "name" || key === "arguments" || key === "caller") continue;
                        try { inspectCandidate(exp[key]); } catch {}
                    }
                } catch {}
            }
        }

        if (moduleCache) {
            for (const m of Object.values(moduleCache)) {
                if (m?.exports) inspectModuleExports(m.exports);
            }
        }

        if (wpRequire?.m && (!foundGifsMap || !token || !restApi)) {
            for (const modId of Object.keys(wpRequire.m)) {
                try {
                    const fnStr = Function.prototype.toString.call(wpRequire.m[modId]);
                    if (
                        fnStr.includes("frecencyWithoutFetchingLatest") ||
                        fnStr.includes("favoriteGifs") ||
                        fnStr.includes("UserSettingsProtoStore") ||
                        fnStr.includes("settings-proto") ||
                        fnStr.includes("refresh-urls") ||
                        (fnStr.includes("getToken") && fnStr.includes("getId"))
                    ) {
                        inspectModuleExports(wpRequire(modId));
                    }
                } catch {}
            }
        }

        return { protoStore, token, restApi, foundGifsMap };
    }

    // =========================================================================
    // 2. REACT FIBER & PROTOBUF API FALLBACKS
    // =========================================================================
    function extractGifsFromReactFiber() {
        const appMount = document.getElementById("app-mount") || document.body;
        const fiberKey = Object.keys(appMount).find(
            (k) => k.startsWith("__reactFiber$") || k.startsWith("__reactContainer$")
        );
        if (!fiberKey) return null;

        let bestMap = null;
        const stack = [appMount[fiberKey]];
        const seen = new WeakSet();
        let steps = 0;

        function checkValue(val) {
            if (!val || typeof val !== "object") return;
            if (isGifMap(val)) {
                if (!bestMap || Object.keys(val).length > Object.keys(bestMap).length) bestMap = val;
                return;
            }
            if (val.favoriteGifs?.gifs && isGifMap(val.favoriteGifs.gifs)) {
                bestMap = val.favoriteGifs.gifs;
                return;
            }
            if (Array.isArray(val) && val.length > 0 && val[0]?.src && ("order" in val[0] || "format" in val[0])) {
                const converted = {};
                val.forEach((item, idx) => {
                    const key = item.url || item.src;
                    if (key) {
                        converted[key] = {
                            format: item.format ?? 1,
                            src: item.src,
                            width: item.width ?? 0,
                            height: item.height ?? 0,
                            order: item.order ?? idx + 1
                        };
                    }
                });
                if (Object.keys(converted).length > Object.keys(bestMap || {}).length) bestMap = converted;
            }
        }

        while (stack.length > 0 && steps < 40000) {
            const node = stack.pop();
            if (!node || seen.has(node)) continue;
            seen.add(node);
            steps++;

            try {
                const props = node.memoizedProps;
                if (props && typeof props === "object") {
                    checkValue(props);
                    for (const v of Object.values(props)) checkValue(v);
                }
                let state = node.memoizedState, sCount = 0;
                while (state && sCount < 15) {
                    checkValue(state.memoizedState);
                    state = state.next;
                    sCount++;
                }
            } catch {}

            if (node.child) stack.push(node.child);
            if (node.sibling) stack.push(node.sibling);
        }

        return bestMap;
    }

    class ProtoReader {
        constructor(bytes) { this.bytes = bytes; this.pos = 0; this.end = bytes.length; }
        eof() { return this.pos >= this.end; }
        readVarint() {
            let result = 0n, shift = 0n;
            while (this.pos < this.end) {
                const b = this.bytes[this.pos++];
                result |= BigInt(b & 0x7f) << shift;
                if ((b & 0x80) === 0) return Number(result);
                shift += 7n;
            }
            return Number(result);
        }
        readBytes() {
            const len = this.readVarint(), start = this.pos;
            this.pos = Math.min(this.end, this.pos + len);
            return this.bytes.subarray(start, this.pos);
        }
        readString() { return new TextDecoder("utf-8").decode(this.readBytes()); }
        skip(wireType) {
            if (wireType === 0) this.readVarint();
            else if (wireType === 1) this.pos += 8;
            else if (wireType === 2) this.pos += this.readVarint();
            else if (wireType === 5) this.pos += 4;
        }
    }

    function decodeFrecencyProtoBase64(b64String) {
        const binStr = atob(b64String);
        const bytes = new Uint8Array(binStr.length);
        for (let i = 0; i < binStr.length; i++) bytes[i] = binStr.charCodeAt(i);

        const gifsMap = {};
        const root = new ProtoReader(bytes);

        function parseFavoriteGif(gifBytes) {
            const r = new ProtoReader(gifBytes);
            const gif = { format: 1, src: "", width: 0, height: 0, order: 0 };
            while (!r.eof()) {
                const tag = r.readVarint(), fieldNo = tag >>> 3, wireType = tag & 7;
                if (fieldNo === 1 && wireType === 0) gif.format = r.readVarint();
                else if (fieldNo === 2 && wireType === 2) gif.src = r.readString();
                else if (fieldNo === 3 && wireType === 0) gif.width = r.readVarint();
                else if (fieldNo === 4 && wireType === 0) gif.height = r.readVarint();
                else if (fieldNo === 5 && wireType === 0) gif.order = r.readVarint();
                else r.skip(wireType);
            }
            return gif;
        }

        function parseMapEntry(entryBytes) {
            const r = new ProtoReader(entryBytes);
            let key = "", value = null;
            while (!r.eof()) {
                const tag = r.readVarint(), fieldNo = tag >>> 3, wireType = tag & 7;
                if (fieldNo === 1 && wireType === 2) key = r.readString();
                else if (fieldNo === 2 && wireType === 2) value = parseFavoriteGif(r.readBytes());
                else r.skip(wireType);
            }
            if (key && value) gifsMap[key] = value;
        }

        function parseFavoriteGifsContainer(containerBytes) {
            const r = new ProtoReader(containerBytes);
            while (!r.eof()) {
                const tag = r.readVarint(), fieldNo = tag >>> 3, wireType = tag & 7;
                if (fieldNo === 1 && wireType === 2) parseMapEntry(r.readBytes());
                else r.skip(wireType);
            }
        }

        while (!root.eof()) {
            const tag = root.readVarint(), fieldNo = tag >>> 3, wireType = tag & 7;
            if (fieldNo === 2 && wireType === 2) parseFavoriteGifsContainer(root.readBytes());
            else root.skip(wireType);
        }

        return gifsMap;
    }

    // =========================================================================
    // 3. LOCATE FAVORITE GIFS
    // =========================================================================
    const { protoStore, token, restApi, foundGifsMap } = getDiscordInternals();
    let rawGifs = foundGifsMap || protoStore?.frecencyWithoutFetchingLatest?.favoriteGifs?.gifs;

    if (!rawGifs || Object.keys(rawGifs).length === 0) {
        rawGifs = extractGifsFromReactFiber();
    }

    if (!rawGifs || Object.keys(rawGifs).length === 0) {
        try {
            let settingsB64 = null;
            if (restApi?.get) {
                const res = await restApi.get({ url: "/users/@me/settings-proto/2" });
                settingsB64 = (res?.body || res)?.settings;
            } else if (token) {
                const resp = await fetch("/api/v9/users/@me/settings-proto/2", {
                    headers: { "Authorization": token }
                });
                if (resp.ok) settingsB64 = (await resp.json())?.settings;
            }
            if (settingsB64) rawGifs = decodeFrecencyProtoBase64(settingsB64);
        } catch (err) {
            console.error("[GIF JSON Exporter] Failed to fetch settings-proto/2:", err);
        }
    }

    if (!rawGifs || Object.keys(rawGifs).length === 0) {
        console.error("[GIF JSON Exporter] Could not locate Favorite GIFs.");
        return;
    }

    // =========================================================================
    // 4. HIGH-QUALITY URL CLEANER & MP4 -> GIF URL UPGRADER
    // =========================================================================
    function isDiscordCdnAttachment(url) {
        if (!url) return false;
        try {
            const parsed = new URL(url);
            const host = parsed.hostname.toLowerCase();
            if (host === "cdn.discordapp.com" || host === "media.discordapp.net") {
                const path = parsed.pathname.toLowerCase();
                return path.startsWith("/attachments/") || path.startsWith("/ephemeral-attachments/");
            }
        } catch {}
        return false;
    }

    function getHighQualityGifUrl(url) {
        if (!url) return url;
        try {
            let parsed = new URL(url);
            const host = parsed.hostname.toLowerCase();

            // 1. Unwrap Discord external proxy URLs (images-ext-1.discordapp.net/external/...)
            if (host.endsWith(".discordapp.net") && parsed.pathname.startsWith("/external/")) {
                const match = parsed.pathname.match(/^\/external\/[^/]+\/(https?)\/(.+)$/);
                if (match) parsed = new URL(`${match[1]}://${match[2]}`);
            }

            const currentHost = parsed.hostname.toLowerCase();

            // 2. Clean Discord CDN / Media Attachment URLs for 100% original file quality
            if (currentHost === "cdn.discordapp.com" || currentHost === "media.discordapp.net") {
                parsed.hostname = "cdn.discordapp.com";
                for (const p of ["format", "animated", "width", "height", "quality", "size"]) {
                    parsed.searchParams.delete(p);
                }
                return parsed.toString();
            }

            // 3. Upgrade Tenor MP4/low-res URLs -> Full-HD .GIF (AAAAC)
            if (currentHost.includes("tenor.com")) {
                const cleanPath = parsed.pathname.replace(/^\/m\//, "/");
                const tenorMatch = cleanPath.match(/^\/([A-Za-z0-9_-]+?)AAA[A-Za-z0-9_-]{2}\/([^/]+?)\.(mp4|webm|webp|gif)$/i);
                if (tenorMatch) {
                    parsed.hostname = "media.tenor.com";
                    parsed.pathname = `/${tenorMatch[1]}AAAAC/${tenorMatch[2]}.gif`;
                    parsed.search = "";
                    return parsed.toString();
                }
                if (/\.mp4$/i.test(cleanPath)) {
                    parsed.pathname = cleanPath.replace(/\.mp4$/i, ".gif");
                    parsed.search = "";
                    return parsed.toString();
                }
            }

            // 4. Upgrade Giphy MP4 -> Full-HD .GIF
            if (currentHost.includes("giphy.com")) {
                if (/\/(?:giphy|200w?|100w?|giphy-preview|giphy-downsized[^/]*)\.mp4$/i.test(parsed.pathname)) {
                    parsed.pathname = parsed.pathname.replace(
                        /\/(?:giphy|200w?|100w?|giphy-preview|giphy-downsized[^/]*)\.mp4$/i,
                        "/giphy.gif"
                    );
                    parsed.search = "";
                    return parsed.toString();
                }
                if (/\.mp4$/i.test(parsed.pathname)) {
                    parsed.pathname = parsed.pathname.replace(/\.mp4$/i, ".gif");
                    parsed.search = "";
                    return parsed.toString();
                }
            }

            // 5. Upgrade Imgur .mp4 / .gifv -> .gif
            if (currentHost.includes("imgur.com") && /\.(mp4|gifv)$/i.test(parsed.pathname)) {
                parsed.pathname = parsed.pathname.replace(/\.(mp4|gifv)$/i, ".gif");
                parsed.search = "";
                return parsed.toString();
            }

            return parsed.toString();
        } catch {
            return url;
        }
    }

    function getAttachmentBaseKey(url) {
        try { return new URL(url).pathname; } catch { return url; }
    }

    // =========================================================================
    // 5. AUTO-REFRESH EXPIRING DISCORD CDN URLS
    // =========================================================================
    async function refreshDiscordAttachmentUrls(urls) {
        const refreshedMap = new Map();
        if (!urls.length) return refreshedMap;

        const BATCH_SIZE = 50;
        console.log(`%c[GIF JSON Exporter] Refreshing ${urls.length} expiring Discord CDN links...`, "color:#FEE75C;");

        for (let i = 0; i < urls.length; i += BATCH_SIZE) {
            const batch = urls.slice(i, i + BATCH_SIZE);
            try {
                let resData = null;
                if (restApi?.post) {
                    const res = await restApi.post({
                        url: "/attachments/refresh-urls",
                        body: { attachment_urls: batch }
                    });
                    resData = res?.body || res;
                } else if (token) {
                    const resp = await fetch("/api/v9/attachments/refresh-urls", {
                        method: "POST",
                        headers: { "Authorization": token, "Content-Type": "application/json" },
                        body: JSON.stringify({ attachment_urls: batch })
                    });
                    if (resp.ok) resData = await resp.json();
                }

                if (resData?.refreshed_urls) {
                    for (const item of resData.refreshed_urls) {
                        if (item.original && item.refreshed) {
                            const cleanRefreshed = getHighQualityGifUrl(item.refreshed);
                            refreshedMap.set(item.original, cleanRefreshed);
                            refreshedMap.set(getAttachmentBaseKey(item.original), cleanRefreshed);
                        }
                    }
                }
            } catch (err) {
                console.warn(`[GIF JSON Exporter] Batch refresh warning (${i}-${i + batch.length}):`, err);
            }
        }
        return refreshedMap;
    }

    const urlsToRefreshSet = new Set();
    for (const [key, val] of Object.entries(rawGifs)) {
        const src = val?.src || key;
        if (isDiscordCdnAttachment(src)) urlsToRefreshSet.add(getHighQualityGifUrl(src));
        if (isDiscordCdnAttachment(key)) urlsToRefreshSet.add(getHighQualityGifUrl(key));
    }

    const refreshedUrlsMap = await refreshDiscordAttachmentUrls([...urlsToRefreshSet]);

    // =========================================================================
    // 6. BUILD CLEAN `src`-ONLY LIST & DOWNLOAD .JSON IMMEDIATELY
    // =========================================================================
    const sortedEntries = Object.entries(rawGifs).sort(
        ([, a], [, b]) => (a?.order ?? 999999) - (b?.order ?? 999999)
    );

    const srcList = [];

    for (const [key, val] of sortedEntries) {
        const rawSrc = val?.src || key;
        const isDiscord = isDiscordCdnAttachment(rawSrc) || isDiscordCdnAttachment(key);

        let bestSrc = rawSrc;
        if (isDiscord) {
            let candidateUrl = isDiscordCdnAttachment(rawSrc) ? rawSrc : key;
            if (/\.gif(\?|$)/i.test(key) && /\.mp4(\?|$)/i.test(rawSrc) && isDiscordCdnAttachment(key)) {
                candidateUrl = key;
            }
            const cleanCandidate = getHighQualityGifUrl(candidateUrl);
            bestSrc =
                refreshedUrlsMap.get(cleanCandidate) ||
                refreshedUrlsMap.get(getAttachmentBaseKey(cleanCandidate)) ||
                cleanCandidate;
        } else {
            bestSrc = getHighQualityGifUrl(rawSrc);
            if (/\.mp4(\?|$)/i.test(bestSrc) && /\.gif(\?|$)/i.test(key)) {
                bestSrc = getHighQualityGifUrl(key);
            }
        }

        srcList.push(bestSrc);
    }

    // Trigger JSON download containing ONLY the array of `src` URLs
    const jsonString = JSON.stringify(srcList, null, 2);
    const blob = new Blob([jsonString], { type: "application/json" });
    const blobUrl = URL.createObjectURL(blob);
    const a = document.createElement("a");
    a.href = blobUrl;
    a.download = "discord-favorite-gifs-fresh.json";
    document.body.appendChild(a);
    a.click();
    a.remove();
    setTimeout(() => URL.revokeObjectURL(blobUrl), 10000);

    console.log(
        `%c[GIF JSON Exporter] ✓ Exported ${srcList.length} high-quality GIF src URLs to discord-favorite-gifs-fresh.json!`,
        "color:#57F287;font-weight:bold;font-size:14px;"
    );
})();
```

</details>

---
### Tool 2: Discord Animated Link Optimizer & ZIP Exporter (Bookmarklet)


Scrapes any opened `.json` or text file containing GIF/WebP URLs in your browser, resizes each image to **128x128**, recompresses/crushes heavy frames to strictly comply with Discord's **<256 KB upload limit**, and compiles everything into a single `discord_gifs_webp.zip` file.

#### How to Use:
1. Open any raw `.json` file containing image links (such as `emojis.json` or `discord-favorite-gifs-fresh.json`) directly in a browser tab.
2. Either create a browser bookmark and paste the snippet below into the **URL** field, or paste the snippet directly into the DevTools Console.
3. The on-screen overlay will indicate downloading, crushing/compressing tiers, and final ZIP packaging.

#### Bookmarklet Script (`Discord_Links to Animated(Bookmark).js`):
<details>
<summary>Click to expand script</summary>

```javascript
javascript:(function(){var P=String.fromCharCode(37);var old=document.getElementById('pi7ModalOverlay');if(old)old.remove();var rawText=document.body.innerText.replace(/\\\//g,'/');var regex=/https?:\/\/[^"'\s<>\\]+?\.(?:gif|webp)(?:\?[^"'\s<>\\]*)?/gi;var matches=rawText.match(regex);var urls=matches?[...new Set(matches)]:[];var ov=document.createElement('div');ov.id='pi7ModalOverlay';ov.style='position:fixed;top:0;left:0;right:0;bottom:0;background:rgba(0,0,0,0.65);z-index:999999;display:flex;align-items:center;justify-content:center;font-family:system-ui,-apple-system,sans-serif;color:rgb(33,37,41);';var card=document.createElement('div');card.style='background:rgb(255,255,255);width:550px;max-height:92vh;overflow-y:auto;border-radius:10px;padding:22px 26px;box-shadow:0 10px 30px rgba(0,0,0,0.4);font-size:14px;line-height:1.4;';card.innerHTML='<div style="display:flex;justify-content:space-between;align-items:center;border-bottom:1px solid rgb(225,228,232);padding-bottom:12px;margin-bottom:14px;"><div style="font-size:17px;font-weight:700;color:rgb(45,55,115);">Discord GIF / WebP Batch Optimizer</div><div style="background:rgb(232,240,254);color:rgb(26,115,232);padding:4px 10px;border-radius:12px;font-size:12px;font-weight:600;">Found '+urls.length+' links</div></div><div style="display:flex;justify-content:space-between;align-items:center;padding:8px 0;border-bottom:1px solid rgb(240,240,240);"><label style="font-weight:500;">Compression level</label><select id="pi7Preset" style="padding:6px 10px;border:1px solid rgb(206,212,218);border-radius:5px;font-size:13px;background:rgb(255,255,255);"><option value="high" selected>High Compression (Small File Size)</option><option value="discord">Discord 128x128 Crisp (Balanced)</option><option value="extreme">Extreme Crusher (For 4MB-30MB GIFs)</option><option value="low">Low Compression (Max Quality)</option><option value="custom">Custom Settings</option></select></div><div style="display:flex;justify-content:space-between;align-items:center;padding:8px 0;border-bottom:1px solid rgb(240,240,240);"><label style="font-weight:500;">Show Advance Settings</label><input type="checkbox" id="pi7ShowAdv" checked style="width:16px;height:16px;accent-color:rgb(13,110,253);cursor:pointer;"></div><div style="display:flex;justify-content:space-between;align-items:center;padding:8px 0;margin-bottom:10px;"><div style="padding-right:10px;"><div style="font-weight:500;">Fast mode (use all CPU cores)</div><div style="font-size:11px;color:rgb(108,117,125);">2-3x faster on large GIFs. Turn it off if you want the smallest possible file.</div></div><input type="checkbox" id="pi7FastMode" style="width:16px;height:16px;accent-color:rgb(13,110,253);cursor:pointer;"></div><div id="pi7AdvBox" style="border:1px solid rgb(222,226,230);border-radius:8px;padding:14px 16px;background:rgb(252,253,254);"><div style="display:flex;justify-content:space-between;align-items:center;padding-bottom:10px;border-bottom:1px solid rgb(238,238,238);"><div><div style="font-weight:500;">Lossy Compression</div><div style="font-size:11px;color:rgb(108,117,125);">Up to 8000. Recommended range is 0-500.</div></div><input type="number" id="pi7Lossy" value="59" min="0" max="8000" style="width:85px;padding:5px 8px;border:1px solid rgb(206,212,218);border-radius:4px;"></div><div style="display:flex;justify-content:space-between;align-items:center;padding:10px 0;border-bottom:1px solid rgb(238,238,238);"><div><div style="font-weight:500;">Reduce Colors In GIF</div><div style="font-size:11px;color:rgb(108,117,125);">256 colours max. 140-200 keeps files small.</div></div><input type="number" id="pi7Colors" value="140" min="2" max="256" style="width:85px;padding:5px 8px;border:1px solid rgb(206,212,218);border-radius:4px;"></div><div style="display:flex;justify-content:space-between;align-items:center;padding:10px 0;border-bottom:1px solid rgb(238,238,238);"><div><div style="font-weight:500;">Scale GIF (Base: Discord 128x128)</div><div id="pi7ScaleText" style="font-size:11px;color:rgb(108,117,125);">Base size is <b>128 X 128</b>. After scaling at <b>74'+P+'</b> width & height will be <b>95 X 95</b></div></div><div style="display:flex;align-items:center;gap:6px;"><input type="number" id="pi7Scale" value="74" min="15" max="100" style="width:75px;padding:5px 8px;border:1px solid rgb(206,212,218);border-radius:4px;"><span>'+P+'</span></div></div><div style="display:flex;justify-content:space-between;align-items:center;padding:10px 0;border-bottom:1px solid rgb(238,238,238);"><div style="font-weight:500;">Skip Frames</div><input type="checkbox" id="pi7SkipChk" checked style="width:16px;height:16px;accent-color:rgb(13,110,253);cursor:pointer;"></div><div style="display:flex;justify-content:space-between;align-items:center;padding:10px 0;border-bottom:1px solid rgb(238,238,238);"><div><div style="font-weight:500;">Remove Every</div><div id="pi7SkipText" style="font-size:11px;color:rgb(108,117,125);">Remove Every <b>4th</b> frame from GIF.<br>Currently GIF have <span style="color:rgb(40,167,69);font-weight:700;">100</span> frames. After skipping there will be <span style="color:rgb(220,53,69);font-weight:700;">75</span> frames in gif.</div></div><div style="display:flex;align-items:center;gap:6px;"><input type="number" id="pi7SkipN" value="4" min="2" max="20" style="width:70px;padding:5px 8px;border:1px solid rgb(206,212,218);border-radius:4px;"><span style="font-size:12px;color:rgb(108,117,125);">Nth Frame</span></div></div><div style="display:flex;justify-content:space-between;align-items:center;padding:10px 0;border-bottom:1px solid rgb(238,238,238);"><div><div style="font-weight:500;">Add Delay In Frames</div><div style="font-size:11px;color:rgb(108,117,125);">Slows a fast GIF by adding delay where frames were removed.</div></div><input type="checkbox" id="pi7AddDelay" style="width:16px;height:16px;accent-color:rgb(13,110,253);cursor:pointer;"></div><div style="display:flex;justify-content:space-between;align-items:center;padding-top:10px;"><div><div style="font-weight:500;color:rgb(45,55,115);">Extra Auto-Clamp If Still >256KB</div><div style="font-size:11px;color:rgb(108,117,125);">Leave unchecked to use ONLY your exact settings above without overrides.</div></div><input type="checkbox" id="pi7Auto256" style="width:16px;height:16px;accent-color:rgb(13,110,253);cursor:pointer;"></div></div><div id="pi7StatusArea" style="display:none;margin-top:14px;padding:10px 14px;border-radius:6px;background:rgb(241,243,245);font-weight:600;font-size:13px;text-align:center;color:rgb(33,37,41);">Ready</div><div style="display:flex;justify-content:center;gap:12px;margin-top:16px;"><button id="pi7StartBtn" style="background:rgb(59,89,152);color:rgb(255,255,255);border:none;padding:10px 24px;border-radius:5px;font-weight:600;font-size:14px;cursor:pointer;">Optimize GIF & Download ZIP</button><button id="pi7CloseBtn" style="background:rgb(222,226,230);color:rgb(52,58,64);border:none;padding:10px 16px;border-radius:5px;font-weight:600;font-size:14px;cursor:pointer;">Close</button></div>';ov.appendChild(card);document.body.appendChild(ov);var elPreset=document.getElementById('pi7Preset'),elShowAdv=document.getElementById('pi7ShowAdv'),elAdvBox=document.getElementById('pi7AdvBox'),elFast=document.getElementById('pi7FastMode'),elLossy=document.getElementById('pi7Lossy'),elColors=document.getElementById('pi7Colors'),elScale=document.getElementById('pi7Scale'),elScaleTxt=document.getElementById('pi7ScaleText'),elSkipChk=document.getElementById('pi7SkipChk'),elSkipN=document.getElementById('pi7SkipN'),elSkipTxt=document.getElementById('pi7SkipText'),elAddDelay=document.getElementById('pi7AddDelay'),elAuto256=document.getElementById('pi7Auto256'),elStatus=document.getElementById('pi7StatusArea'),elStart=document.getElementById('pi7StartBtn'),elClose=document.getElementById('pi7CloseBtn');function updLabels(setCustom){if(setCustom)elPreset.value='custom';var sc=Math.max(15,Math.min(100,parseInt(elScale.value)||100));var px=Math.max(16,Math.round(128*sc/100));elScaleTxt.innerHTML='Base size is <b>128 X 128</b>. After scaling at <b>'+sc+P+'</b> width & height will be <b>'+px+' X '+px+'</b>';var n=Math.max(2,Math.min(20,parseInt(elSkipN.value)||4));var kept=elSkipChk.checked?(100-Math.floor(100/n)):100;var suf=n===2?'nd':(n===3?'rd':'th');elSkipTxt.innerHTML='Remove Every <b>'+n+suf+'</b> frame from GIF.<br>Currently GIF have <span style="color:rgb(40,167,69);font-weight:700;">100</span> frames. After skipping there will be <span style="color:rgb(220,53,69);font-weight:700;">'+kept+'</span> frames in gif.';}elLossy.oninput=function(){updLabels(true);};elColors.oninput=function(){updLabels(true);};elScale.oninput=function(){updLabels(true);};elSkipN.oninput=function(){updLabels(true);};elSkipChk.onchange=function(){updLabels(true);};elAddDelay.onchange=function(){updLabels(true);};elShowAdv.onchange=function(){elAdvBox.style.display=elShowAdv.checked?'block':'none';};elPreset.onchange=function(){var v=elPreset.value;if(v==='high'){elLossy.value=59;elColors.value=140;elScale.value=74;elSkipChk.checked=true;elSkipN.value=4;elAddDelay.checked=false;}else if(v==='discord'){elLossy.value=45;elColors.value=180;elScale.value=100;elSkipChk.checked=true;elSkipN.value=4;elAddDelay.checked=false;}else if(v==='extreme'){elLossy.value=160;elColors.value=96;elScale.value=74;elSkipChk.checked=true;elSkipN.value=2;elAddDelay.checked=false;}else if(v==='low'){elLossy.value=15;elColors.value=256;elScale.value=100;elSkipChk.checked=false;elSkipN.value=5;elAddDelay.checked=false;}updLabels(false);};elClose.onclick=function(){ov.remove();};function isMult(a,b){return(a-Math.floor(a/b)*b)===0;}function decodeGIF89(u8,targetW,targetH){if(u8.length<13||u8[0]!==71||u8[1]!==73||u8[2]!==70)return null;var pos=6,lsW=u8[pos]|(u8[pos+1]<<8),lsH=u8[pos+2]|(u8[pos+3]<<8),packed=u8[pos+4];pos+=7;var gct=null;if(packed&128){var gSize=2<<(packed&7);gct=u8.subarray(pos,pos+gSize*3);pos+=gSize*3;}var canv=new Uint8ClampedArray(lsW*lsH*4),prevCanv=new Uint8ClampedArray(lsW*lsH*4),frames=[];var delayCs=4,dispose=0,transFlag=0,transIdx=0;var needScale=(lsW!==targetW||lsH!==targetH),sCanv=null,sCtx=null,tCanv=null,tCtx=null;if(needScale){sCanv=document.createElement('canvas');sCanv.width=lsW;sCanv.height=lsH;sCtx=sCanv.getContext('2d');tCanv=document.createElement('canvas');tCanv.width=targetW;tCanv.height=targetH;tCtx=tCanv.getContext('2d',{willReadFrequently:true});tCtx.imageSmoothingEnabled=true;tCtx.imageSmoothingQuality='high';}var prefix=new Int32Array(4096),suffix=new Uint8Array(4096),pixelStack=new Uint8Array(4097);while(pos<u8.length){var b=u8[pos++];if(b===59)break;if(b===33){var label=u8[pos++];if(label===249){var bSize=u8[pos++],gP=u8[pos];dispose=(gP>>2)&7;transFlag=gP&1;delayCs=u8[pos+1]|(u8[pos+2]<<8);transIdx=u8[pos+3];pos+=bSize+1;}else{while(pos<u8.length){var sL=u8[pos++];if(sL===0)break;pos+=sL;}}}else if(b===44){var fx=u8[pos]|(u8[pos+1]<<8),fy=u8[pos+2]|(u8[pos+3]<<8),fw=u8[pos+4]|(u8[pos+5]<<8),fh=u8[pos+6]|(u8[pos+7]<<8),iP=u8[pos+8];pos+=9;var ct=gct;if(iP&128){var lSize=2<<(iP&7);ct=u8.subarray(pos,pos+lSize*3);pos+=lSize*3;}var interlace=(iP&64)!==0,minCodeSize=u8[pos++],chunks=[],totLzw=0;while(pos<u8.length){var cL=u8[pos++];if(cL===0)break;chunks.push(u8.subarray(pos,pos+cL));totLzw+=cL;pos+=cL;}var lzwData=new Uint8Array(totLzw),off=0;for(var c=0;c<chunks.length;c++){lzwData.set(chunks[c],off);off+=chunks[c].length;}var nPix=fw*fh,pixels=new Uint8Array(nPix),clearCode=1<<minCodeSize,eoiCode=clearCode+1,codeSize=minCodeSize+1,codeMask=(1<<codeSize)-1,avail=clearCode+2,oldCode=-1,first=0;for(var k=0;k<clearCode;k++)suffix[k]=k;var datum=0,bits=0,pi=0,top=0,bi=0;while(pi<nPix){if(top===0){if(bits<codeSize){if(bi>=totLzw)break;datum|=lzwData[bi++]<<bits;bits+=8;continue;}var code=datum&codeMask;datum>>=codeSize;bits-=codeSize;if(code>avail||code===eoiCode)break;if(code===clearCode){codeSize=minCodeSize+1;codeMask=(1<<codeSize)-1;avail=clearCode+2;oldCode=-1;continue;}if(oldCode===-1){pixelStack[top++]=suffix[code];oldCode=code;first=code;continue;}var inCode=code;if(code===avail){pixelStack[top++]=first;code=oldCode;}while(code>clearCode){pixelStack[top++]=suffix[code];code=prefix[code];}first=suffix[code];pixelStack[top++]=first;if(avail<4096){prefix[avail]=oldCode;suffix[avail]=first;avail++;if((avail&codeMask)===0&&avail<4096){codeSize++;codeMask+=avail;}}oldCode=inCode;}top--;pixels[pi++]=pixelStack[top];}if(dispose===3)prevCanv.set(canv);var pass=0,inc=8,py=0;for(var y=0;y<fh;y++){var drawY=interlace?py:y;if(interlace){py+=inc;if(py>=fh){pass++;if(pass===1){py=4;inc=8;}else if(pass===2){py=2;inc=4;}else{py=1;inc=2;}}}var cy=fy+drawY;if(cy<0||cy>=lsH)continue;var srcR=y*fw,dstR=cy*lsW;for(var x=0;x<fw;x++){var cx=fx+x;if(cx<0||cx>=lsW)continue;var idx=pixels[srcR+x];if(transFlag&&idx===transIdx)continue;var cOff=idx*3,dOff=(dstR+cx)*4;if(ct&&cOff+2<ct.length){canv[dOff]=ct[cOff];canv[dOff+1]=ct[cOff+1];canv[dOff+2]=ct[cOff+2];canv[dOff+3]=255;}}}var fData;if(!needScale){fData=new Uint8ClampedArray(canv);}else{sCtx.putImageData(new ImageData(new Uint8ClampedArray(canv),lsW,lsH),0,0);tCtx.clearRect(0,0,targetW,targetH);var sc=Math.min(targetW/lsW,targetH/lsH),dw=Math.round(lsW*sc),dh=Math.round(lsH*sc);tCtx.drawImage(sCanv,Math.round((targetW-dw)/2),Math.round((targetH-dh)/2),dw,dh);fData=tCtx.getImageData(0,0,targetW,targetH).data;}frames.push({data:fData,ms:(delayCs<=1?4:delayCs)*10});if(dispose===2){for(var y=0;y<fh;y++){var cy2=fy+y;if(cy2<0||cy2>=lsH)continue;var dstR2=cy2*lsW;for(var x=0;x<fw;x++){var cx2=fx+x;if(cx2<0||cx2>=lsW)continue;var dOff2=(dstR2+cx2)*4;canv[dOff2]=0;canv[dOff2+1]=0;canv[dOff2+2]=0;canv[dOff2+3]=0;}}}else if(dispose===3){canv.set(prevCanv);}delayCs=4;dispose=0;transFlag=0;}else{break;}}return frames.length>0?frames:null;}function encodeGIF(frames,w,h,maxActualColors,lossyVal){var colorBits=maxActualColors<=4?2:(maxActualColors<=8?3:(maxActualColors<=16?4:(maxActualColors<=32?5:(maxActualColors<=64?6:(maxActualColors<=128?7:8)))));var tableSlots=1<<colorBits,targetCols=Math.min(tableSlots,Math.max(2,maxActualColors));var deltaSq=lossyVal*22,lzwSq=lossyVal*18,spatialSq=lossyVal*10;var hist=new Int32Array(32768),sumR=new Int32Array(32768),sumG=new Int32Array(32768),sumB=new Int32Array(32768);for(var f=0;f<frames.length;f++){var d=frames[f].data;for(var p=0;p<d.length;p+=4){if(d[p+3]>=128){var c=((d[p]>>3)<<10)|((d[p+1]>>3)<<5)|(d[p+2]>>3);hist[c]++;sumR[c]+=d[p];sumG[c]+=d[p+1];sumB[c]+=d[p+2];}}}var active=[],cR=new Uint8Array(32768),cG=new Uint8Array(32768),cB=new Uint8Array(32768);for(var c=0;c<32768;c++){var cnt=hist[c];if(cnt>0){active.push(c);cR[c]=(sumR[c]/cnt)|0;cG[c]=(sumG[c]/cnt)|0;cB[c]=(sumB[c]/cnt)|0;}}function makeBox(list){var rMin=255,rMax=0,gMin=255,gMax=0,bMin=255,bMax=0,tot=0;for(var i=0;i<list.length;i++){var c=list[i],w2=hist[c],r=cR[c],g=cG[c],b=cB[c];tot+=w2;if(r<rMin)rMin=r;if(r>rMax)rMax=r;if(g<gMin)gMin=g;if(g>gMax)gMax=g;if(b<bMin)bMin=b;if(b>bMax)bMax=b;}var dr=(rMax-rMin)*2,dg=(gMax-gMin)*3,db=(bMax-bMin)*1.5,span=Math.max(dr,dg,db);var axis=dr>=dg&&dr>=db?0:(dg>=db?1:2);return{list:list,tot:tot,axis:axis,score:list.length>1?span*Math.pow(tot,0.45):0};}var boxes=active.length>0?[makeBox(active)]:[];while(boxes.length<targetCols-1){var bestIdx=-1,bestScore=0;for(var i=0;i<boxes.length;i++){if(boxes[i].score>bestScore){bestScore=boxes[i].score;bestIdx=i;}}if(bestIdx===-1)break;var bx=boxes[bestIdx],ax=bx.axis;bx.list.sort(function(a,b){return ax===0?cR[a]-cR[b]:(ax===1?cG[a]-cG[b]:cB[a]-cB[b]);});var half=bx.tot/2,acc=0,mid=1;for(var i=0;i<bx.list.length-1;i++){acc+=hist[bx.list[i]];if(acc>=half){mid=i+1;break;}}boxes[bestIdx]=makeBox(bx.list.slice(0,mid));boxes.push(makeBox(bx.list.slice(mid)));}var pal=[[0,0,0]];for(var i=0;i<boxes.length;i++){var bl=boxes[i].list,tr=0,tg=0,tb=0,tc=0;for(var j=0;j<bl.length;j++){var c=bl[j];tr+=sumR[c];tg+=sumG[c];tb+=sumB[c];tc+=hist[c];}pal.push(tc>0?[(tr/tc)|0,(tg/tc)|0,(tb/tc)|0]:[0,0,0]);}var actualLen=pal.length;while(pal.length<tableSlots)pal.push([0,0,0]);var pDistSq=new Int32Array(tableSlots*tableSlots);for(var i=1;i<actualLen;i++){for(var j=1;j<actualLen;j++){var drP=pal[i][0]-pal[j][0],dgP=pal[i][1]-pal[j][1],dbP=pal[i][2]-pal[j][2];pDistSq[(i<<colorBits)|j]=drP*drP*2+dgP*dgP*3+dbP*dbP;}}var lut=new Uint8Array(32768);for(var i=0;i<active.length;i++){var c=active[i],r=cR[c],g=cG[c],b=cB[c],bestP=1,bestD=1e9;for(var p=1;p<actualLen;p++){var dr=r-pal[p][0],dg=g-pal[p][1],db=b-pal[p][2],d2=dr*dr*2+dg*dg*3+db*db;if(d2<bestD){bestD=d2;bestP=p;if(d2===0)break;}}lut[c]=bestP;}var nPix=w*h,fRect=[],disp=new Uint8Array(frames.length),canvI=new Uint8Array(nPix),canvR=new Uint8Array(nPix),canvG=new Uint8Array(nPix),canvB=new Uint8Array(nPix);for(var f=0;f<frames.length;f++){var d=frames[f].data,rawI=new Uint8Array(nPix),canKeep=f>0;for(var p=0;p<nPix;p++){var p4=p*4;if(d[p4+3]<128){rawI[p]=0;if(canvI[p]!==0)canKeep=false;}else{rawI[p]=lut[((d[p4]>>3)<<10)|((d[p4+1]>>3)<<5)|(d[p4+2]>>3)];}}if(canKeep){disp[f-1]=1;var minX=w,minY=h,maxX=-1,maxY=-1,fullI=new Uint8Array(nPix);for(var y=0;y<h;y++){var row=y*w;for(var x=0;x<w;x++){var p=row+x,idx=rawI[p],p4=p*4;var drC=d[p4]-canvR[p],dgC=d[p4+1]-canvG[p],dbC=d[p4+2]-canvB[p];var cdSq=drC*drC*2+dgC*dgC*3+dbC*dbC;if(canvI[p]===idx||(canvI[p]!==0&&idx!==0&&cdSq<=deltaSq)){fullI[p]=0;}else{if(x>0&&fullI[p-1]!==0&&idx!==0){var prevIdx=fullI[p-1];if(pDistSq[(idx<<colorBits)|prevIdx]<=spatialSq)idx=prevIdx;}fullI[p]=idx;canvI[p]=idx;canvR[p]=pal[idx][0];canvG[p]=pal[idx][1];canvB[p]=pal[idx][2];if(x<minX)minX=x;if(x>maxX)maxX=x;if(y<minY)minY=y;if(y>maxY)maxY=y;}}}if(maxX<minX){fRect.push({x:0,y:0,w:1,h:1,data:new Uint8Array([0])});}else{var bw=maxX-minX+1,bh=maxY-minY+1,subI=new Uint8Array(bw*bh),dst=0;for(var y=minY;y<=maxY;y++){var row=y*w;for(var x=minX;x<=maxX;x++)subI[dst++]=fullI[row+x];}fRect.push({x:minX,y:minY,w:bw,h:bh,data:subI});}}else{if(f>0)disp[f-1]=2;for(var p=0;p<nPix;p++){var idx=rawI[p];if(p>0&&rawI[p-1]!==0&&idx!==0&&pDistSq[(idx<<colorBits)|rawI[p-1]]<=spatialSq){idx=rawI[p-1];rawI[p]=idx;}canvI[p]=idx;if(idx>0){canvR[p]=pal[idx][0];canvG[p]=pal[idx][1];canvB[p]=pal[idx][2];}}fRect.push({x:0,y:0,w:w,h:h,data:rawI});}}disp[frames.length-1]=2;var out=[71,73,70,56,57,97,w&255,w>>8,h&255,h>>8,0x80|((colorBits-1)<<4)|(colorBits-1),0,0];for(var i=0;i<tableSlots;i++){var pt=pal[i];out.push(pt[0],pt[1],pt[2]);}out.push(33,255,11,78,69,84,83,67,65,80,69,50,46,48,3,1,0,0,0);var table=new Int32Array(4096<<colorBits),head=new Int16Array(4096),next=new Int16Array(4096),sym=new Uint8Array(4096);for(var f=0;f<frames.length;f++){var delayCs=frames[f].cs,pk=(disp[f]<<2)|1,rc=fRect[f];out.push(33,249,4,pk,delayCs&255,delayCs>>8,0,0,44,rc.x&255,rc.x>>8,rc.y&255,rc.y>>8,rc.w&255,rc.w>>8,rc.h&255,rc.h>>8,0);var minCode=Math.max(2,colorBits);out.push(minCode);var clearCode=1<<minCode,eoiCode=clearCode+1,nextCode=eoiCode+1,codeSize=minCode+1;table.fill(-1);head.fill(-1);var sub=[],bitBuf=0,bitCnt=0;var emit=function(code){bitBuf|=code<<bitCnt;bitCnt+=codeSize;while(bitCnt>=8){sub.push(bitBuf&255);bitBuf>>=8;bitCnt-=8;if(sub.length===255){out.push(255);for(var s=0;s<255;s++)out.push(sub[s]);sub.length=0;}}};emit(clearCode);var idxArr=rc.data,prefix=idxArr[0],rLen=idxArr.length;for(var p=1;p<rLen;p++){var k=idxArr[p],key=(prefix<<colorBits)|k,hit=table[key];if(hit===-1&&lzwSq>0&&k!==0){var bestNode=-1,bestErr=lzwSq;for(var nd=head[prefix];nd!==-1;nd=next[nd]){var cSym=sym[nd];if(cSym!==0){var dSq=pDistSq[(k<<colorBits)|cSym];if(dSq<=bestErr){bestErr=dSq;bestNode=nd;}}}if(bestNode!==-1)hit=bestNode;}if(hit!==-1){prefix=hit;}else{emit(prefix);if(nextCode<4096){if(nextCode===(1<<codeSize)&&codeSize<12)codeSize++;sym[nextCode]=k;next[nextCode]=head[prefix];head[prefix]=nextCode;table[key]=nextCode++;}else{emit(clearCode);table.fill(-1);head.fill(-1);codeSize=minCode+1;nextCode=eoiCode+1;}prefix=k;}}emit(prefix);emit(eoiCode);if(bitCnt>0)sub.push(bitBuf&255);if(sub.length>0){out.push(sub.length);for(var s=0;s<sub.length;s++)out.push(sub[s]);}out.push(0);}out.push(59);return new Uint8Array(out);}async function processOne(url,idx,tot,cfg){var baseW=Math.max(16,Math.round(128*cfg.scale/100));var srcBlob=null;try{var rUrl='https://wsrv.nl/?url='+encodeURIComponent(url)+'&w='+baseW+'&h='+baseW+'&fit=contain&n=-1&output=gif';var resp=await fetch(rUrl);if(resp.ok)srcBlob=await resp.blob();}catch(e){}if(!srcBlob){try{var r2=await fetch(url);if(r2.ok)srcBlob=await r2.blob();}catch(e){}if(!srcBlob){var r3=await fetch('https://corsproxy.io/?'+encodeURIComponent(url));if(r3.ok)srcBlob=await r3.blob();}}if(!srcBlob)throw new Error('Download failed');var buf=await srcBlob.arrayBuffer();var u8=new Uint8Array(buf);var raw=decodeGIF89(u8,baseW,baseW);if(!raw&&window.ImageDecoder){var hdr=String.fromCharCode.apply(null,u8.subarray(0,12));var mime=hdr.startsWith('GIF')?'image/gif':(hdr.slice(8,12)==='WEBP'?'image/webp':'image/png');var dec=new ImageDecoder({data:buf,type:mime});await dec.tracks.ready;var total=dec.tracks.selectedTrack.frameCount||1;raw=[];var c=document.createElement('canvas');c.width=baseW;c.height=baseW;var ctx=c.getContext('2d',{willReadFrequently:true});for(var i=0;i<total;i++){var fr=await dec.decode({frameIndex:i}),vf=fr.image;var d=vf.duration?vf.duration/1000:40;if(d<=10)d=33;ctx.clearRect(0,0,baseW,baseW);var vw=vf.displayWidth||vf.codedWidth||baseW,vh=vf.displayHeight||vf.codedHeight||baseW;var sc=Math.min(baseW/vw,baseW/vh),dw=Math.round(vw*sc),dh=Math.round(vh*sc);ctx.drawImage(vf,Math.round((baseW-dw)/2),Math.round((baseW-dh)/2),dw,dh);raw.push({data:ctx.getImageData(0,0,baseW,baseW).data,ms:d});vf.close();}dec.close();}if(!raw||raw.length===0)return{blob:srcBlob,ext:'gif',origF:1,keptF:1};for(var f=0;f<raw.length;f++){var px=raw[f].data;for(var p=0;p<px.length;p+=4){var a=px[p+3];if(a>=95&&a<250){px[p]=((px[p]*a+49*(255-a))/255)|0;px[p+1]=((px[p+1]*a+51*(255-a))/255)|0;px[p+2]=((px[p+2]*a+56*(255-a))/255)|0;px[p+3]=255;}else if(a<95){px[p+3]=0;}}}var totalMs=0;for(var i=0;i<raw.length;i++)totalMs+=raw[i].ms;var stepFrames=[];for(var i=0;i<raw.length;i++){if(cfg.skip&&raw.length>2&&isMult(i+1,cfg.nth))continue;stepFrames.push({data:raw[i].data,ms:raw[i].ms});}if(stepFrames.length===0)stepFrames.push(raw[0]);var encInput=[];var uniformAddedCs=Math.max(2,Math.round((totalMs/stepFrames.length)/10));for(var j=0;j<stepFrames.length;j++){var fCs=cfg.addDelay?uniformAddedCs:Math.max(2,Math.round(stepFrames[j].ms/10));encInput.push({data:stepFrames[j].data,cs:fCs});}var lastBytes=encodeGIF(encInput,baseW,baseW,cfg.colors,cfg.lossy);if(cfg.auto256&&lastBytes.byteLength>255000){var boost=[{c:Math.min(cfg.colors,96),l:Math.max(cfg.lossy,180),st:1},{c:Math.min(cfg.colors,64),l:Math.max(cfg.lossy,350),st:2},{c:48,l:Math.max(cfg.lossy,600),st:2}];for(var b=0;b<boost.length;b++){var subF=[];for(var k=0;k<encInput.length;k+=boost[b].st)subF.push(encInput[k]);lastBytes=encodeGIF(subF,baseW,baseW,boost[b].c,boost[b].l);if(lastBytes.byteLength<=255000)break;}}return{blob:new Blob([lastBytes],{type:'image/gif'}),ext:'gif',origF:raw.length,keptF:stepFrames.length};}async function runBatch(){if(urls.length===0){elStatus.style.display='block';elStatus.innerText='No GIF or WEBP links found on this page!';return;}elStart.disabled=true;elStart.style.opacity='0.6';elStatus.style.display='block';elStatus.style.background='rgb(232,240,254)';elStatus.style.color='rgb(26,115,232)';var cfg={lossy:Math.max(0,parseInt(elLossy.value)||0),colors:Math.max(2,Math.min(256,parseInt(elColors.value)||140)),scale:Math.max(15,Math.min(100,parseInt(elScale.value)||100)),skip:elSkipChk.checked,nth:Math.max(2,parseInt(elSkipN.value)||4),addDelay:elAddDelay.checked,auto256:elAuto256.checked,fast:elFast.checked};var pxDim=Math.max(16,Math.round(128*cfg.scale/100));var zip=new JSZip(),done=0,ok=0,totalBytes=0,concurrency=cfg.fast?3:1,cursor=0;async function worker(){while(cursor<urls.length){var myIdx=cursor++;try{var res=await processOne(urls[myIdx],myIdx+1,urls.length,cfg);zip.file('discord_'+(myIdx+1)+'.'+res.ext,res.blob);totalBytes+=res.blob.size;ok++;done++;var kb=(res.blob.size/1024).toFixed(1);elStatus.innerText='Optimized '+done+'/'+urls.length+' — '+pxDim+'x'+pxDim+'px | Frames: '+res.keptF+'/'+res.origF+' | Size: '+kb+' KB';}catch(e){console.error('Failed:',urls[myIdx],e);done++;}}}var workers=[];for(var w=0;w<concurrency;w++)workers.push(worker());await Promise.all(workers);if(ok===0){elStatus.style.background='rgb(248,215,218)';elStatus.style.color='rgb(114,28,36)';elStatus.innerText='Failed to download images.';elStart.disabled=false;elStart.style.opacity='1';return;}elStatus.innerText='Packing discord_gifs_webp.zip...';zip.generateAsync({type:'blob',compression:'DEFLATE',compressionOptions:{level:6}}).then(function(content){var a=document.createElement('a');a.href=URL.createObjectURL(content);a.download='discord_gifs_webp.zip';document.body.appendChild(a);a.click();document.body.removeChild(a);var avgKb=(totalBytes/ok/1024).toFixed(1);elStatus.style.background='rgb(212,237,218)';elStatus.style.color='rgb(21,87,36)';elStatus.innerText='Done! '+ok+' GIFs ('+pxDim+'x'+pxDim+'px, Avg: '+avgKb+' KB, Lossy: '+cfg.lossy+', Colors: '+cfg.colors+')';elStart.disabled=false;elStart.style.opacity='1';});}elStart.onclick=function(){if(window.JSZip){runBatch();return;}elStatus.style.display='block';elStatus.innerText='Loading JSZip Engine...';var script=document.createElement('script');script.src='https://cdnjs.cloudflare.com/ajax/libs/jszip/3.10.1/jszip.min.js';script.onload=runBatch;script.onerror=function(){elStatus.innerText='Blocked! Save .json to PC, open in a new tab, and click bookmark.';};document.head.appendChild(script);};})();
```

</details>

---

### Tool 3: Server Emoji CDN Extractor & Exporter

Extracts all custom emojis from a server's emoji management screen, sets their resolution to a standard size (`256px`), preserves GIF animations, logs the URLs, copies them to your clipboard, and automatically downloads `emojis.json`.

#### How to Use:
1. Open Discord Web and navigate to **Server Settings** $\rightarrow$ **Emoji**.
2. Open DevTools Console (`Ctrl + Shift + I`).
3. Paste the code below and press Enter.

#### Script (`CDN Extractor(Discord_Emojis).js`):
<details>
<summary>Click to expand script</summary>

```javascript
(function exportEmojiUrls() {
    const urls = new Set();

    const formatEmojiUrl = (rawUrl) => {
        try {
            const url = new URL(rawUrl);
            const isAnimated = url.searchParams.get('animated') === 'true' || url.pathname.endsWith('.gif');
            
            if (isAnimated) {
                url.pathname = url.pathname.replace(/\.(webp|png)$/, '.gif');
            } else {
                url.pathname = url.pathname.replace(/\.webp$/, '.png');
            }

            url.search = '?size=256';
            return url.toString();
        } catch (e) {
            return rawUrl;
        }
    };

    // Scope to settings rows first so it doesn't grab random emojis from background chat
    const emojiRows = document.querySelectorAll('[class*="emojiRow"]');
    const containers = emojiRows.length > 0 ? emojiRows : [document];

    containers.forEach(container => {
        // 1. Check standard <img> tags
        container.querySelectorAll('img[src*="/emojis/"]').forEach(img => {
            urls.add(formatEmojiUrl(img.src));
        });

        // 2. Check background-image divs
        container.querySelectorAll('[style*="/emojis/"]').forEach(div => {
            const match = div.style.backgroundImage.match(/url\(["']?(.*?)["']?\)/);
            if (match && match[1]) {
                urls.add(formatEmojiUrl(match[1]));
            }
        });
    });

    const urlArray = Array.from(urls);

    if (urlArray.length === 0) {
        console.warn("⚠️ No emojis found! Make sure you are inside Server Settings > Emoji.");
        return;
    }

    // Format as a simple JSON array of strings
    const jsonOutput = JSON.stringify(urlArray, null, 2);

    // 1. Print URLs to console & copy to clipboard
    console.log(urlArray.join('\n'));
    if (typeof copy === 'function') copy(jsonOutput);

    // 2. Automatically download the emojis.json file
    const blob = new Blob([jsonOutput], { type: 'application/json' });
    const link = document.createElement('a');
    link.href = URL.createObjectURL(blob);
    link.download = 'emojis.json';
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
    URL.revokeObjectURL(link.href);

    console.log(`%c✅ Exported ${urlArray.length} URLs to emojis.json (and copied to clipboard)!`, 'color: #43b581; font-weight: bold;');
})();
```

</details>

---

### Tool 4: Server Emoji Bulk Deletion Tool

Quickly purges all custom emojis from a server you own or manage. It extracts the authentication token from Discord's internal webpack chunk to issue direct REST `DELETE` API requests with automatic rate-limit handling (`HTTP 429`), falling back to simulated UI button clicks if necessary.

#### How to Use:
1. Open Discord Web and navigate to **Server Settings** $\rightarrow$ **Emoji** of the server you wish to clear.
2. Open DevTools Console (`Ctrl + Shift + I`).
3. Paste the code below and hit Enter. Confirm the browser dialog prompt.
4. *(Optional)* To abort deletion at any point, paste `window.stopDeleting = true` into the console.

#### Script (`Discord (Delete Emojis_FromCreatedServer).js`):
<details>
<summary>Click to expand script</summary>

```javascript
(async function clearAllServerEmojis() {
    const sleep = (ms) => new Promise(r => setTimeout(r, ms));

    // 1. Get Server ID from the URL
    const guildId = window.location.pathname.split('/')[2];

    // 2. Get Auth Token (to send real DELETE requests to Discord)
    let token = null;
    try {
        window.webpackChunkdiscord_app.push([[Symbol()], {}, req => {
            for (const mod of Object.values(req.c)) {
                const exp = mod?.exports;
                if (!exp) continue;
                for (const key of Reflect.ownKeys(exp)) {
                    try {
                        const val = exp[key];
                        if (val && typeof val.getToken === 'function') {
                            const t = val.getToken();
                            if (typeof t === 'string' && t.length > 20) token = t;
                        }
                    } catch {}
                }
            }
        }]);
        window.webpackChunkdiscord_app.pop();
    } catch {}

    if (!token) {
        try {
            const iframe = document.createElement('iframe');
            document.body.appendChild(iframe);
            token = JSON.parse(iframe.contentWindow.localStorage.token || 'null');
            iframe.remove();
        } catch {}
    }

    // 3. Collect all emojis in this server (ID, Name, and the exact right-side Delete button)
    const emojiMap = new Map();
    const rows = Array.from(document.querySelectorAll('[class*="emojiRow"]')).filter(
        row => !row.parentElement?.closest('[class*="emojiRow"]')
    );

    rows.forEach(row => {
        // Extract Emoji ID from background-image or img src
        let rawUrl = row.querySelector('img[src*="/emojis/"]')?.src || '';
        if (!rawUrl) {
            const bg = row.querySelector('[style*="/emojis/"]')?.style?.backgroundImage || '';
            const m = bg.match(/url\(["']?(.*?)["']?\)/);
            if (m) rawUrl = m[1];
        }

        const idMatch = rawUrl.match(/\/emojis\/(\d+)/);
        if (!idMatch) return;

        const id = idMatch[1];
        const name = row.querySelector('input')?.value || `emoji_${id}`;

        // IMPORTANT: Only select the remove button (never the first button which opens "Edit Emoji")
        const buttons = Array.from(row.querySelectorAll('button, [role="button"]'));
        const deleteBtn =
            row.querySelector('[class*="emojiRemove"]') ||
            (buttons.length > 1 ? buttons[buttons.length - 1] : null);

        if (!emojiMap.has(id)) {
            emojiMap.set(id, { id, name, deleteBtn });
        }
    });

    const emojis = Array.from(emojiMap.values());

    if (emojis.length === 0) {
        console.warn("⚠️ No emojis found! Make sure you are inside Server Settings > Emoji.");
        return;
    }

    if (!confirm(`⚠️ Are you sure you want to permanently delete ALL (${emojis.length}) emojis in this server?`)) {
        console.log("❌ Emoji deletion cancelled.");
        return;
    }

    console.log(
        `%c🗑️ Deleting ${emojis.length} emojis...\n%cTo stop at any time, paste: window.stopDeleting = true`,
        'color: #f04747; font-weight: bold; font-size: 13px;',
        'color: #faa61a;'
    );

    window.stopDeleting = false;
    let deletedCount = 0;

    for (let i = 0; i < emojis.length; i++) {
        if (window.stopDeleting) {
            console.log("🛑 Deletion stopped by user.");
            break;
        }

        const emoji = emojis[i];

        // Method A: Direct Discord API DELETE (fastest & never opens the Edit modal)
        if (token && guildId) {
            const res = await fetch(`https://discord.com/api/v9/guilds/${guildId}/emojis/${emoji.id}`, {
                method: 'DELETE',
                headers: { 'Authorization': token }
            });

            if (res.status === 204 || res.ok) {
                deletedCount++;
                console.log(`🗑️ [${deletedCount}/${emojis.length}] Deleted :${emoji.name}:`);
                await sleep(1500);
            } else if (res.status === 429) {
                const data = await res.json().catch(() => ({}));
                const waitMs = Math.ceil((data.retry_after || 4) * 1000) + 500;
                console.warn(`⏳ Rate limited by Discord! Waiting ${(waitMs / 1000).toFixed(1)}s...`);
                await sleep(waitMs);
                i--; // Retry this exact emoji
            } else {
                console.error(`❌ Failed to delete :${emoji.name}: (HTTP ${res.status})`);
            }
        }
        // Method B: Fallback to clicking ONLY the right-side remove button
        else if (emoji.deleteBtn) {
            emoji.deleteBtn.click();
            deletedCount++;
            console.log(`🗑️ [${deletedCount}/${emojis.length}] Deleted :${emoji.name}:`);
            await sleep(2000);
        }
    }

    console.log(`%c✅ Done! Deleted ${deletedCount}/${emojis.length} emojis.`, 'color: #43b581; font-weight: bold; font-size: 14px;');
})();
```

</details>


---

## 2. Video & Audio Changer (`script.js` &rarr; Bookmarking)

<details>
<summary>Click to expand / view script code</summary>

```javascript
javascript:(()=>{try{const
    s=window.__mediaBooster=window.__mediaBooster||{ctx:null,gain:null,hooked:new
    WeakSet(),speed:1,volume:1,observing:false,host:null,syncUI:null};const
    g=(k,d)=>{try{const v=parseFloat(localStorage.getItem(k));return
    isNaN(v)?d:v}catch{return%20d}};const%20p=(k,v)=%3E{try{localStorage.setItem(k,String(v))}catch{}};if(!s.init){s.speed=g(%27boostPlaybackSpeed%27,1);s.volume=g(%27boostVolume%27,1);s.init=true}const%20ens=()=%3E{if(!s.ctx&&s.volume%3E1){const%20C=window.AudioContext||window.webkitAudioContext;if(C){s.ctx=new%20C();s.gain=s.ctx.createGain();s.gain.connect(s.ctx.destination)}}if(s.ctx){if(s.ctx.state===%27suspended%27)s.ctx.resume().catch(()=%3E{});if(s.gain)s.gain.gain.value=s.volume}};const%20ok=el=%3E{const%20u=el.currentSrc||el.src;if(!u||u.startsWith(%27blob:%27)||u.startsWith(%27data:%27))return%20true;try{return%20new%20URL(u,location.href).origin===location.origin||Boolean(el.crossOrigin)}catch{return%20false}};const%20app=el=%3E{if(!el||(el.tagName!%27VIDEO%27&&el.tagName!%27AUDIO%27))return;if(s.speed%3E0&&Math.abs(el.playbackRate-s.speed)%3E0.01){try{el.playbackRate=s.speed}catch{}}ens();if(s.hooked.has(el)){el.volume=1}else%20if(s.volume%3E1&&s.ctx&&s.gain&&ok(el)){try{const%20src=s.ctx.createMediaElementSource(el);src.connect(s.gain);s.hooked.add(el);el.volume=1}catch{el.volume=Math.min(1,Math.max(0,s.volume))}}else{el.volume=Math.min(1,Math.max(0,s.volume))}};const%20scan=(r=document)=%3E{if(!r)return;r.querySelectorAll(%27video,audio%27).forEach(app);r.querySelectorAll(%27*%27).forEach(el=%3E{if(el.shadowRoot&&el!==s.host)scan(el.shadowRoot);if(el.tagName===%27IFRAME%27){try{if(el.contentDocument)scan(el.contentDocument)}catch{}}})};const%20upd=(sp,vl,txt=true)=%3E{if(sp!==null&&!isNaN(sp)){s.speed=Math.min(16,Math.max(0.0625,Math.round(sp100)/100));p(%27boostPlaybackSpeed%27,s.speed)}if(vl!==null&&!isNaN(vl)){s.volume=Math.min(10,Math.max(0,Math.round(vl100)/100));p(%27boostVolume%27,s.volume)}ens();scan();if(s.syncUI)s.syncUI(txt)};const%20parse=raw=%3E{const%20str=raw.trim().toLowerCase();if(!str)return;if(str===%27r%27||str===%27reset%27){upd(1,1,false);return}const%20nv=v=%3Ev%3E10?v/100:v;if(/^[v,/]/.test(str)||(str.endsWith(%27%%27)&&!/\s/.test(str))){const%20v=parseFloat(str.replace(/^[v,/]|%/g,%27%27));if(!isNaN(v)&&v%3E=0)upd(null,nv(v),false);return}const%20pts=str.split(/[\s,\/]+/).filter(Boolean);if(pts.length===1){const%20sp=parseFloat(pts[0]);if(!isNaN(sp)&&sp%3E=0.1&&sp%3C=16)upd(sp,null,false)}else%20if(pts.length%3E=2){const%20sp=parseFloat(pts[0]),vr=parseFloat(pts[1].replace(/%/,%27%27));upd(!isNaN(sp)&&sp%3E=0.1&&sp%3C=16?sp:null,!isNaN(vr)&&vr%3E=0?(pts[1].endsWith(%27%%27)?vr/100:nv(vr)):null,false)}};if(!s.observing){s.observing=true;const%20fn=e=%3E{const%20t=e.composedPath?e.composedPath()[0]:e.target;if(t&&(t.tagName===%27VIDEO%27||t.tagName===%27AUDIO%27))app(t)};[%27play%27,%27loadedmetadata%27,%27ratechange%27].forEach(ev=%3Ewindow.addEventListener(ev,fn,true));[%27click%27,%27keydown%27,%27touchstart%27].forEach(ev=%3Ewindow.addEventListener(ev,()=%3E{if(s.ctx&&s.ctx.state===%27suspended%27)s.ctx.resume().catch(()=%3E{})},{capture:true,passive:true}));const%20obs=new%20MutationObserver(ms=%3E{for(const%20m%20of%20ms)for(const%20n%20of%20m.addedNodes)if(n.nodeType===1&&n!==s.host){if(n.tagName===%27VIDEO%27||n.tagName===%27AUDIO%27)app(n);else%20if(n.querySelectorAll){n.querySelectorAll(%27video,audio%27).forEach(app);if(n.shadowRoot)scan(n.shadowRoot)}}});if(document.documentElement)obs.observe(document.documentElement,{childList:true,subtree:true})}ens();scan();if(s.host&&s.host.isConnected){s.host.remove();s.host=null;return}const%20h=(t,a={},...c)=%3E{const%20e=document.createElement(t);for(const[k,v]of%20Object.entries(a)){if(k===%27className%27)e.className=v;else%20if(k.startsWith(%27on%27)&&typeof%20v===%27function%27)e.addEventListener(k.slice(2).toLowerCase(),v);else%20e.setAttribute(k,v)}for(const%20x%20of%20c)if(x!=null)e.appendChild(typeof%20x===%27string%27?document.createTextNode(x):x);return%20e};const%20host=h(%27div%27,{id:%27__media_booster_hud%27});s.host=host;const%20sh=host.attachShadow({mode:%27open%27});const%20st=h(%27style%27,{},%27:host{all:initial;position:fixed;top:18px;right:18px;z-index:2147483647;font-family:system-ui,-apple-system,sans-serif}.card{width:300px;background:rgba(16,16,22,.95);backdrop-filter:blur(12px);color:#f3f4f6;border:1px%20solid%20rgba(255,255,255,.14);border-radius:12px;padding:14px;box-shadow:0%2012px%2032px%20rgba(0,0,0,.55)}.hdr{display:flex;justify-content:space-between;align-items:center;margin-bottom:10px}.title{font-weight:700;font-size:13px;color:#fff}.badges{display:flex;gap:6px;align-items:center}.badge{background:rgba(99,102,241,.22);color:#a5b4fc;border:1px%20solid%20rgba(99,102,241,.4);padding:2px%207px;border-radius:6px;font-size:12px;font-weight:700}.close{background:none;border:none;color:#9ca3af;cursor:pointer;font-size:15px;padding:0%204px}.close:hover{color:#fff}.cmd{width:100%;box-sizing:border-box;background:rgba(0,0,0,.45);border:1.5px%20solid%20rgba(99,102,241,.7);color:#fff;border-radius:8px;padding:8px%2010px;font-size:15px;font-weight:600;outline:none;margin-bottom:8px}.cmd:focus{border-color:#818cf8;box-shadow:0%200%200%203px%20rgba(99,102,241,.25)}.hint{font-size:11px;color:#9ca3af;margin-bottom:10px;display:flex;justify-content:space-between}.row{display:flex;align-items:center;justify-content:space-between;gap:4px;margin-top:6px}.lbl{font-size:11px;color:#9ca3af;font-weight:600;width:42px}.pills{display:flex;gap:4px;flex:1;justify-content:flex-end}.pill{background:rgba(255,255,255,.08);border:1px%20solid%20rgba(255,255,255,.1);color:#d1d5db;border-radius:6px;padding:4px%207px;font-size:11px;font-weight:600;cursor:pointer}.pill:hover{background:rgba(255,255,255,.16);color:#fff}.pill.active{background:#4f46e5;border-color:#6366f1;color:#fff}');const%20sb=h('span',{className:'badge'}),vb=h('span',{className:'badge'}),cls=()=%3E{if(s.host){s.host.remove();s.host=null}};const%20inp=h('input',{className:'cmd',type:'text',placeholder:'e.g.%202%20or%201.5%20200%20or%20v3%20or%20r',onInput:e=%3Eparse(e.target.value)});inp.addEventListener('keydown',e=%3E{e.stopPropagation();if(e.key==='Enter'||e.key==='Escape'){e.preventDefault();cls()}else%20if(e.key==='ArrowUp'||e.key==='ArrowDown'){e.preventDefault();const%20d=e.key==='ArrowUp'?1:-1;if(e.shiftKey)upd(null,s.volume+d*.25,true);else%20upd(s.speed+d*.25,null,true)}});['keyup','keypress'].forEach(ev=%3Einp.addEventListener(ev,e=%3Ee.stopPropagation()));const%20sps=[1,1.25,1.5,2,3],vps=[1,1.5,2,3,4];const%20sBtns=sps.map(x=%3Eh('button',{className:'pill',onClick:()=%3E{upd(x,null,true);inp.focus()}},%60{x}x%60));const%20vBtns=vps.map(x=%3Eh('button',{className:'pill',onClick:()=%3E{upd(null,x,true);inp.focus()}},%60{x100}%%60));s.syncUI=txt=%3E{sb.textContent=%60%E2%9A%A1%20{s.speed}x%60;vb.textContent=%60%F0%9F%94%8A%20{Math.round(s.volume100)}%%60;if(txt)inp.value=%60{s.speed}%20{Math.round(s.volume*100)}%%60;sBtns.forEach((b,i)=%3Eb.classList.toggle('active',Math.abs(s.speed-sps[i])%3C.01));vBtns.forEach((b,i)=%3Eb.classList.toggle('active',Math.abs(s.volume-vps[i])%3C.01))};const%20card=h('div',{className:'card'},h('div',{className:'hdr'},h('div',{className:'title'},'Media%20Booster'),h('div',{className:'badges'},sb,vb,h('button',{className:'close',onClick:cls},'%E2%9C%95'))),inp,h('div',{className:'hint'},h('span',{},'Type%20%222%22,%20%221.5%20200%22,%20%22v3%22,%20%22r%22'),h('span',{},'%E2%86%91%E2%86%93%20Spd%20%E2%80%A2%20%E2%87%A7%E2%86%91%E2%86%93%20Vol')),h('div',{className:'row'},h('span',{className:'lbl'},'Speed'),h('div',{className:'pills'},...sBtns)),h('div',{className:'row'},h('span',{className:'lbl'},'Volume'),h('div',{className:'pills'},...vBtns)));sh.append(st,card);(document.body||document.documentElement).appendChild(host);s.syncUI(true);inp.focus();inp.select()}catch(e){alert('Media%20Booster%20Error:%20'+e.message)}})();
```

</details>

---


## 3. Rotate Selected Photos (`script.js` &rarr; Bookmarking)

<details>
<summary>Click to expand / view script code</summary>

```javascript
javascript:(function(){const cbs=Array.from(document.querySelectorAll('[role="checkbox"][aria-checked="true"]'));if(!cbs.length){alert("Please select at least one photo first!");return;}const ex=document.getElementById('gp-rot-modal');if(ex)ex.remove();const el=(t,c,x)=>{const e=document.createElement(t);if(c)e.style.cssText=c;if(x)e.textContent=x;return e;};const modal=el('div','position:fixed;top:0;left:0;width:100%;height:100%;background:rgba(0,0,0,0.6);z-index:999999;display:flex;align-items:center;justify-content:center;font-family:Google Sans,Roboto,Arial,sans-serif;');modal.id='gp-rot-modal';const box=el('div','background:#fff;padding:24px;border-radius:12px;box-shadow:0 8px 24px rgba(0,0,0,0.3);text-align:center;min-width:300px;');const title=el('h3','margin:0 0 8px;color:#202124;font-size:18px;',`Batch Rotate (${cbs.length} photos)`);const sub=el('p','margin:0 0 14px;color:#5f6368;font-size:14px;','Choose rotation direction:');const lbl=el('label','display:flex;align-items:center;justify-content:center;gap:8px;margin-bottom:16px;font-size:14px;color:#202124;cursor:pointer;background:#f1f3f4;padding:8px;border-radius:6px;font-weight:500;');const chk=document.createElement('input');chk.type='checkbox';chk.checked=true;chk.style.cursor='pointer';lbl.append(chk,document.createTextNode('⚡ Turbo Speed (Fastest)'));const btnWrap=el('div','display:flex;flex-direction:column;gap:10px;');[{l:'↺ 90° Left (Counter-Clockwise)',t:1},{l:'↻ 90° Right (Clockwise)',t:3},{l:'⇅ 180° (Upside Down)',t:2}].forEach(o=>{const b=el('button','padding:12px;border:none;border-radius:8px;background:#1a73e8;color:#fff;font-size:15px;font-weight:500;cursor:pointer;',o.l);b.onclick=()=>run(o.t,chk.checked);btnWrap.appendChild(b);});const cancel=el('button','padding:10px;border:1px solid #dadce0;border-radius:8px;background:#fff;color:#5f6368;font-size:14px;cursor:pointer;margin-top:4px;','Cancel');cancel.onclick=()=>modal.remove();btnWrap.appendChild(cancel);box.append(title,sub,lbl,btnWrap);modal.appendChild(box);document.body.appendChild(modal);async function run(times,turbo){const status=el('div','background:#202124;color:#fff;padding:12px 24px;border-radius:30px;font-size:15px;box-shadow:0 4px 12px rgba(0,0,0,0.4);','Preparing...');modal.replaceChildren(status);modal.style.background='transparent';modal.style.pointerEvents='none';modal.style.alignItems='flex-start';modal.style.paddingTop='20px';const sleep=ms=>new Promise(r=>setTimeout(r,ms));const waitUrl=async(has,max=3500)=>{const s=Date.now();while(Date.now()-s<max){if(location.href.includes('/photo/')===has)return;await sleep(15);}};let hrefs=[];cbs.forEach(cb=>{let a=cb.parentElement.querySelector('a');if(a&&a.getAttribute('href'))hrefs.push(a.getAttribute('href'));});let clr=document.querySelector('[aria-label="Clear selection"]');if(clr){clr.click();}else{cbs.forEach(cb=>cb.click());}const s0=Date.now();while(Date.now()-s0<1500){if(!document.querySelector('[role="checkbox"][aria-checked="true"]'))break;await sleep(20);}await sleep(turbo?100:300);for(let i=0;i<hrefs.length;i++){status.textContent=`⚡ Rotating ${i+1} of ${hrefs.length}...`;let link=null;for(let w=0;w<40;w++){link=document.querySelector(`a[href="${hrefs[i]}"]`);if(link&&link.isConnected)break;await sleep(25);}if(!link)continue;const rect=link.getBoundingClientRect();if(rect.top<0||rect.bottom>window.innerHeight){link.scrollIntoView({block:'center'});await sleep(turbo?60:150);}link.click();await waitUrl(true);await sleep(turbo?180:450);for(let r=0;r<times;r++){document.body.dispatchEvent(new KeyboardEvent('keydown',{key:'R',code:'KeyR',keyCode:82,which:82,shiftKey:true,bubbles:true}));await sleep(turbo?45:200);}await sleep(turbo?260:600);let bks=Array.from(document.querySelectorAll('[aria-label="Back"], [aria-label="Close"]'));let vis=bks.find(b=>b.offsetParent!==null);if(vis){vis.click();}else{document.body.dispatchEvent(new KeyboardEvent('keydown',{key:'Escape',code:'Escape',keyCode:27,which:27,bubbles:true}));}await waitUrl(false);await sleep(turbo?80:300);}status.style.background='#188038';status.textContent=`✓ Done! Rotated ${hrefs.length} photos.`;await sleep(2000);modal.remove();}})();
```

</details>

---

## 4. Course Helper (iGOTKarmayogi) - for Completion (`script.js` &rarr; Bookmarking)


#### How to Use:
1. Access from below of this URL
2. Click On Raw, and `Ctrl + A` to copy the entire code
3. Press F12 or  `Ctrl + Shift + I` and switch to the **Console** tab.
4. Paste the script below and hit Enter. The entire course helper will be automated
https://gist.github.com/rShogun26/b21e3a3460b531a04e058b3362865c48

#### For API: 
Just follow the steps respectively to access the AI features for assessments automation



---