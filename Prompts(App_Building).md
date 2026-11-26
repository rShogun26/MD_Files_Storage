## 𝐜𝐫𝐞𝐝𝐢𝐭𝐬𝐚𝐟𝐞

```text
MASTER UNIVERSAL WORKFLOW (ALL MODELS & ALL TASK TYPES — FOLLOW STRICTLY IN ORDER):
Deliver 100% production-ready work for ANY task (new builds, features, UI/UX, refactors, migrations, or bug fixes) with zero wasted credits and seamless continuation across chats/accounts without needing prior chat history.STEP 1 — PARSE, PLAN & CREATE `TODO_CONTINUATION.md` FIRST:
- Check if `TODO_CONTINUATION.md` (or `*_CONTINUATION.md`) exists.
  * On timeout/crash recovery (`Agent Timeout` / `Model unavailable`) if `.md` is missing/stale, run `git status` / `git diff --stat` ONCE to detect edits, then create/update `TODO_CONTINUATION.md` first.
  * For multi-task or large tasks, ALWAYS create `TODO_CONTINUATION.md` BEFORE editing code. (Skip creating it ONLY for a tiny 1-file fix unless you must pause early.)
- Parse uneven/multi-task input: even if written in 1 messy paragraph, voice-typed, or out of order, extract EVERY request into ordered tasks (`Task 1`, `Task 2`...) logically (foundations/quick updates first, core logic next).
- Keep `TODO_CONTINUATION.md` compact yet 100% self-contained:
  1. `## Goal & Requirements`: Brief summary of each request, expected behavior, and constraints.
  2. `## Task Checklist`: Ordered `- [ ] Task 1: ...`, `- [ ] Task 2: ...` (`- [x]` done, indented `- [ ]` sub-tasks + target file/symbol hints).
  3. `## Changes & Verification`: Concise list of created/modified files/functions, latest check status, and exact next step.

STEP 2 — ZERO-WASTE CONTINUOUS EXECUTION (NEVER BREAK THE APP):
- Resume strictly from the first unchecked `- [ ]` item (never redo `- [x]` items).
- Read ONLY target files directly needed for the active task (MAX 2–3 files per task). NEVER scan the whole repo, guess symbol names, or run `git log`/multi-search loops.
- Write clean modular code for new files; minimal surgical edits for existing files (never rewrite unchanged sections). Check entry points/configs before modifying/deleting shared files. Keep replies very short.
- ALWAYS make real file edits in your turn (never stop with 0 edits or an empty RESUME LOG).
- CONTINUOUS EXECUTION & SMART STOP RULES:
  * NORMAL MODE (Default): Execute consecutive tasks smoothly (`Task 1` -> `Task 2` -> `Task 3`...) in THIS TURN until all tasks finish—do NOT stop early after 1 small task.
  * LOW-CREDIT MODE (Only if user writes `Low-credit mode`): Complete 1 clean task (or 1 safe sub-step if large), then pause cleanly under Case A.
  * HUGE-TASK & LOOP BRAKES: Pause early under Case A ONLY if: (a) you hit ~25–30 total actions on a massive task (avoids timeouts), OR (b) the same error fails twice.
  * SAFE NON-BREAKING MIDWAY RULE: Edit bottom-up (helpers, new components, backend types FIRST; wire into UI/routes/entry points LAST). NEVER pause with dangling imports, half-renamed symbols, or broken callers—the app MUST compile and run cleanly whenever paused.

STEP 3 — CHECKPOINT PROGRESS BEFORE CHECK:
- Before running any check command, update `TODO_CONTINUATION.md` ONCE: flip completed tasks/sub-tasks to `- [x]`, keep/split remaining work as `- [ ]`, and list created/modified files/symbols.

STEP 4 — STRICTLY 1 FAST SYNTAX CHECK ONLY (ZERO BUILD/BROWSER LOOPS):
- Run AT MOST 1 fast syntax/type check at the end (`tsc --noEmit`, `eslint`, or `cargo check`; skip if pure docs).
- FORBIDDEN unless explicitly requested: Playwright/headless browser checks, dev servers, `cargo build`, `--release`, full `wasm`/asset builds, full test suites, or multi-command chains.
- If a check tool (like Cargo) is missing in the sandbox, do NOT retry—run `git diff --check` once instead and note local/CI verification is still needed.

STEP 5 — END OF TURN (CHOOSE EXACTLY ONE):
- CASE A — TASK(S) NOT YET COMPLETED:
  1. Ensure app builds/runs cleanly (fix tiny wiring or revert incomplete hook if Step 4 failed from a pause) and `TODO_CONTINUATION.md` shows `- [x]` done vs `- [ ]` next + target files.
  2. End immediately with a short **RESUME LOG** (`[PAUSED CLEANLY — READY TO CONTINUE / CREATE PR]`).
- CASE B — ALL TASKS 100% COMPLETED:
  1. Verify every checkbox in `TODO_CONTINUATION.md` is `- [x]`, then DELETE `TODO_CONTINUATION.md` (and any `*_CONTINUATION.md` files).
  2. Write NO continuation steps, state **TASK COMPLETED**, and list manual user steps (review diff, local/CI verify if needed, pull GitHub, PR, deploy).
```

---

## //𝐍𝐞𝐰-𝐓𝐚𝐬𝐤 𝐅𝐨𝐨𝐭𝐞𝐫 (𝐏𝐚𝐬𝐭𝐞 𝐚𝐭 𝐭𝐡𝐞 𝐛𝐨𝐭𝐭𝐨𝐦 𝐨𝐟 𝐀𝐍𝐘 𝐧𝐞𝐰 𝐭𝐚𝐬𝐤 — 𝐒𝐚𝐦𝐞 𝐂𝐡𝐚𝐭 𝐎𝐑 𝐍𝐞𝐰 𝐂𝐡𝐚𝐭):

```text
Follow active creditsafe Steps 1–5 strictly: parse into TODO_CONTINUATION.md first (skip only if tiny 1-file fix), execute tasks consecutively with minimal file reads & surgical edits, checkpoint - [x] BEFORE running AT MOST 1 fast syntax check (NO browser checks/builds), and end cleanly with Case A or Case B.``

//add low credit mode (if below $1) -> Low-credit mode.

---

## //𝐂𝐡𝐚𝐭 𝐂𝐨𝐧𝐭𝐢𝐧𝐮𝐚𝐭𝐢𝐨𝐧 & 𝐂𝐫𝐚𝐬𝐡 𝐑𝐞𝐜𝐨𝐯𝐞𝐫𝐲 (𝐏𝐚𝐬𝐭𝐞 𝐀𝐋𝐎𝐍𝐄 𝐭𝐨 𝐫𝐞𝐬𝐮𝐦𝐞 𝐢𝐧 𝐒𝐚𝐦𝐞 𝐂𝐡𝐚𝐭, 𝐍𝐞𝐰 𝐂𝐡𝐚𝐭, 𝐎𝐑 𝐍𝐞𝐰 𝐀𝐜𝐜𝐨𝐮𝐧𝐭):

```text
Resume strictly per active creditsafe Steps 1–5: read TODO_CONTINUATION.md (or git status once if the last turn timed out/dropped), pick up from the first unchecked - [ ] item, continue smoothly with minimal reads & surgical edits, checkpoint - [x] BEFORE running AT MOST 1 fast syntax check (NO browser checks/builds), and end cleanly with Case A or Case B.

//add low credit mode (if below $1) -> Low-credit mode.
```

# //𝐒𝐭𝐨𝐩 𝐂𝐨𝐝𝐞 𝐚𝐧𝐝 𝐒𝐚𝐯𝐞 𝐚𝐬 𝐆𝐈𝐓:
```text
STOP all new coding. Check git status and git diff --stat once: if any partial edit leaves the app broken, cleanly revert only that incomplete edit. Update TODO_CONTINUATION.md with what is done (- [x]) vs remaining (- [ ]), commit and push to the working branch, and print a short RESUME LOG so I can continue in a new account.
```
---

## //𝐜𝐫𝐞𝐚𝐭𝐢𝐧𝐠 & 𝐦𝐞𝐫𝐠𝐢𝐧𝐠 𝐨𝐟 𝐏𝐑 𝐢𝐧 𝐠𝐢𝐭𝐡𝐮𝐛 𝐫𝐞𝐩𝐨 (𝐟𝐨𝐫 𝐔𝐑𝐋 𝐁𝐚𝐬𝐞𝐝):

```bash
printf '%s\n' '#!/bin/bash' 'printf "Token: " && read -r T' 'printf "URL: " && read -r U_IN' 'C="${U_IN%/}"; C="${C%.git}"' 'U=$(echo "$C" | cut -d"/" -f4)' 'R=$(echo "$C" | cut -d"/" -f5)' 'B=$(echo "$C" | cut -d"/" -f7-)' 'P="https://"; G="github.com"' '[ -z "$B" ] && B=$(curl -s -H "Authorization: Bearer $T" "${P}api.${G}/repos/${U}/${R}" | grep -o "\"default_branch\": *\"[^\"]*\"" | cut -d"\"" -f4)' 'B="${B:-main}"; BR="v0-$(date +%s)"' 'echo "Repo: $U/$R | Branch: $B"' 'git init -q && git config user.name "$U" && git config user.email "$U@users.noreply.github.com"' 'printf "\npr.sh\ntarget/\ndist/\nnode_modules/\n.next/\n.env*\n" >> .gitignore' 'git remote remove origin 2>/dev/null || true' 'git remote add origin "${P}${T}@${G}/${U}/${R}.git"' 'git fetch -q origin "$B" && git reset --soft -q "origin/$B" && git checkout -q -B "$BR"' 'git add . && git commit -q -m "Update from v0" && git push -q -u origin "$BR"' 'PR=$(curl -s -X POST -H "Authorization: Bearer $T" "${P}api.${G}/repos/${U}/${R}/pulls" -d "{\"title\":\"Update from v0\",\"head\":\"$BR\",\"base\":\"$B\"}")' 'NUM=$(echo "$PR" | grep -o "\"number\": *[0-9]*" | head -n1 | tr -dc "0-9")' 'echo "PR #$NUM -> ${P}${G}/${U}/${R}/pull/${NUM}"' 'sleep 2 && curl -s -X PUT -H "Authorization: Bearer $T" "${P}api.${G}/repos/${U}/${R}/pulls/${NUM}/merge" -d "{\"merge_method\":\"squash\"}" | grep -o "\"merged\": *true" && git push -q origin --delete "$BR"' 'git remote set-url origin "${P}${G}/${U}/${R}.git"' > pr.sh && bash pr.sh
```

---

## //𝐟𝐨𝐫 𝐜𝐫𝐞𝐚𝐭𝐢𝐧𝐠 𝐳𝐢𝐩 𝐟𝐢𝐥𝐞 𝐞𝐧𝐭𝐢𝐫𝐞𝐥𝐲 𝐮𝐬𝐢𝐧𝐠 𝐯𝐞𝐫𝐜𝐞𝐥 (𝐯𝟎) 𝐭𝐞𝐫𝐦𝐢𝐧𝐚𝐥 -> 𝐞𝐧𝐭𝐢𝐫𝐞 𝐩𝐫𝐨𝐣𝐞𝐜𝐭 𝐢𝐬 𝐝𝐨𝐰𝐧𝐥𝐨𝐚𝐝𝐞𝐝

```bash
zip -r project.zip . -x "node_modules/*" "target/*" ".git/*" ".next/*" "dist/*" "build/*" "__pycache__/*" "venv/*" ".venv/*" "vendor/*" "project.zip"
```

---

## //𝐟𝐨𝐫 𝐝𝐞𝐥𝐞𝐭𝐢𝐧𝐠 𝐭𝐡𝐞 𝐳𝐢𝐩 𝐟𝐢𝐥𝐞 𝐮𝐬𝐢𝐧𝐠 𝐯𝐞𝐫𝐜𝐞𝐥 (𝐯𝟎) 𝐭𝐞𝐫𝐦𝐢𝐧𝐚𝐥 

```bash
rm -f project.zip
```

## //𝐌𝐞𝐫𝐠𝐢𝐧𝐠 𝐜𝐡𝐚𝐧𝐠𝐞𝐬 -> 𝐃𝐢𝐫𝐞𝐜𝐭 𝐩𝐮𝐬𝐡𝐢𝐧𝐠 𝐢𝐧𝐭𝐨 𝐭𝐡𝐞 𝐜𝐮𝐫𝐫𝐞𝐧𝐭 𝐛𝐫𝐚𝐧𝐜𝐡 𝐜𝐨𝐧𝐧𝐞𝐜𝐭𝐞𝐝 (𝐭𝐨𝐤𝐞𝐧𝐬 𝐫𝐞𝐪𝐮𝐢𝐫𝐞𝐝):

```bash
printf '%s\n' '#!/bin/bash' 'printf "Token: " && read -r T' 'P="https://"; G="github.com"' 'REM=$(git remote get-url origin | sed "s|\.git$||")' 'U=$(echo "$REM" | awk -F"/" "{print \$(NF-1)}")' 'R=$(echo "$REM" | awk -F"/" "{print \$NF}")' 'CUR=$(git branch --show-current)' 'echo "Repo: $U/$R | Active Branch: $CUR"' 'printf "\npush.sh\ntarget/\ndist/\nnode_modules/\n.next/\n.env*\n" >> .git/info/exclude' 'git add . && git commit -q -m "Update from v0" --allow-empty' 'git remote set-url origin "${P}${T}@${G}/${U}/${R}.git"' 'if [[ "$CUR" == v0/* ]]; then' '  B=$(curl -s -H "Authorization: Bearer $T" "${P}api.${G}/repos/${U}/${R}" | grep -o "\"default_branch\": *\"[^\"]*\"" | cut -d"\"" -f4)' '  B="${B:-rust-branch}"' '  git push -q -u origin "$CUR"' '  PR=$(curl -s -X POST -H "Authorization: Bearer $T" "${P}api.${G}/repos/${U}/${R}/pulls" -d "{\"title\":\"Update from v0\",\"head\":\"$CUR\",\"base\":\"$B\"}")' '  NUM=$(echo "$PR" | grep -o "\"number\": *[0-9]*" | head -n1 | tr -dc "0-9")' '  echo "PR #$NUM -> ${P}${G}/${U}/${R}/pull/${NUM}"' '  sleep 2 && curl -s -X PUT -H "Authorization: Bearer $T" "${P}api.${G}/repos/${U}/${R}/pulls/${NUM}/merge" -d "{\"merge_method\":\"squash\"}" | grep -o "\"merged\": *true" && git push -q origin --delete "$CUR"' 'else' '  git push -q -u origin "$CUR" && echo "Pushed directly to $CUR!"' 'fi' 'git remote set-url origin "${P}${G}/${U}/${R}.git"' > push.sh && bash push.sh
```








---

## (𝗙𝗼𝗿 𝗩𝟬.𝗔𝗣𝗣)

```text
𝗦𝗬𝗦𝗧𝗘𝗠 𝗢𝗩𝗘𝗥𝗥𝗜𝗗𝗘 — 𝗦𝗜𝗡𝗚𝗟𝗘-𝗙𝗜𝗟𝗘 𝗠𝗢𝗗𝗘:
1. Create ONLY ONE file in the workspace / file tree using the exact filename and extension specified below.
2. DO NOT scaffold a Next.js, React, or web app project (no package.json, layout, routing, components, or config files).
3. DO NOT start a dev server, run build/preview commands, or open a live preview.
4. Write the complete, exact requested content directly into that single file—no placeholders, truncation, or mock data.
5. In the chat response, output NO code, NO preamble, and NO explanation—just confirm the file was created (1 short sentence max) and stop immediately.
6. DO NOT list, read, inspect, or edit any preloaded workspace files (app/, package.json, etc.), and DO NOT run any terminal/bash commands—write the target file in ONE single file-write step with no extra tool calls or verification loops.
TARGET FILENAME: physics_lab_guide.tex
TASK: [Insert your exact request here] 
```

Notes for V0: 
1. Ensure that, + → Instructions, "Be Concise" is enabled
2. Choose the model + give the 6 point instruction (above)

---

## 𝗣𝗥𝗢𝗠𝗣𝗧𝗦:

```text
Okay, so now are there any sort of garbage files over here? Like you have seen all the bugs which are fixed and you have done also. Now can you please tell me that in this application if any files are there which are completely useless for this application and which is not at all required? Or any dead codes present in this web app code entirely go through it, check if any code lines have dead codes or unusable, useless ones which are not required by the app itself
```

```text
Now, lastly, I will please tell you to do is that if in this application if any small bugs, the tiniest bugs, whatever bugs and errors which is present in this code entirely, check through each and every code and check if any errors and problems are there, please debug it and fix it accordingly so that it runs like the entire application runs smoothly and very optimized way without having any issues or errors respectively. 
```

```text
1st do is delete all the files which is present in the root directly, except the [name of the zip file].zip
2nd is then the [name of the zip file].zip, extract it, put all the contents, every contents to the root directory
3rd and last is finally delete the [name of the zip file].zip
```

---

## 𝗙𝗢𝗥 𝗙𝗜𝗡𝗗𝗜𝗡𝗚 𝗘𝗥𝗥𝗢𝗥𝗦 𝗨𝗦𝗜𝗡𝗚 𝗟𝗜𝗚𝗛𝗧𝗛𝗢𝗨𝗦𝗘 𝗥𝗘𝗣𝗢𝗥𝗧 (𝗨𝗦𝗜𝗡𝗚 𝗖𝗛𝗥𝗢𝗠𝗘𝗗𝗘𝗩 𝗧𝗢𝗢𝗟𝗦 - 𝗙𝟭𝟮):

```text
please find the ways for performance segment to boost the performance
see the performance tab under the lighthouse report. tell the bugs and the fixes that can be made and i will directly paste it in google ai studio chat so that it can further use the report sort and fix it accordingly to maximise my performance, both both desktop and mobile version without affecting my design and also please do not go with the accessibility case like whatever task like you can read through the entire lighthouse report and it is literally written for the performance tab so please according to the performance mode only check through the entire report and tell me the bugs and the fixes side by side so that I can paste it in the Google AI studio chat accordingly.. Look through the diagnostics and the insights if using both of these you can result in the exact ways of actually the problems which are there or the red color which is literally mentioned over here the ways to fix it
```

---

## Text-Font TTF Handwritten Generator:

```text
Generate me the image of from this the glyph form, like having the chart table typo, like having all alphabets, numbers, the punctuation, GLYPHS, referring to the image uploaded to determine the custom font and it should be 100% like 
hence, Refer to this picture mainly for its text style and write down the below text pasted in the exact 100% text present on the image shared 
the glyph chart will comprise mainly of -> Uppercase, LowerCase, Numbers & Punctuations
```
