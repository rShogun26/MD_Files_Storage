## 𝐜𝐫𝐞𝐝𝐢𝐭𝐬𝐚𝐟𝐞

```text
Deliver production-ready work for ANY task while staying UNDER the user's `$` budget. Stopping midway is REQUIRED at the action cap—and the app MUST stay non-breaking and compilable.
STEP 1 — PARSE, SEGREGATE & CREATE `TODO_CONTINUATION.md` FIRST (BEFORE ANY EDIT):
- Check if `TODO_CONTINUATION.md` (or `*_CONTINUATION.md`) exists. If not, ALWAYS create `TODO_CONTINUATION.md` FIRST—before editing any files (Case B deletes it when finished).
- Parse uneven/multi-task prompts: even if written in one messy paragraph, voice-typed, or out of order, extract EVERY request into ordered tasks (`Task 1`, `Task 2`, `Task 3`...) logically (setup/quick updates first, core logic next).
- Keep `TODO_CONTINUATION.md` compact yet 100% self-contained assuming zero chat history next session:
  1. `## Goal & Requirements`: Brief summary, expected behavior, constraints.
  2. `## Task Checklist`: Ordered `- [ ] Task 1: ...`, `- [ ] Task 2: ...` (`- [x]` done, indented `- [ ]` sub-tasks + target file hints).
  3. `## Changes & Verification`: Concise list of created/modified files/functions and latest check status.
- Read ONLY needed target files (MAX 2–3/task; MAX 1–2 if `< $0.80`). NEVER scan whole repo, guess symbol names, or run `git log`/search loops.

STEP 2 — MULTI-TASK / SUB-STEP LOOP (STAY UNDER BUDGET & NEVER BREAK THE APP):
- Resume strictly from the first unchecked `- [ ]` item (never redo `- [x]` items).
- Write clean modular code for new files; minimal surgical edits for existing files. Check entry points/configs before editing/deleting shared files. Keep replies short.
- ALWAYS make real edits in your turn (never stop with 0 edits).
- STRICT TOTAL ACTION CEILING (EVERY READ + SEARCH + EDIT + COMMAND COMBINED):
  * `$2.50+`: Finish ALL tasks if feasible, or loop consecutive tasks up to MAX 28–35 total actions.
  * `$1.26 – $2.49`: Loop consecutive tasks up to MAX 20–26 total actions.
  * `$0.60 – $1.25`: Loop consecutive tasks up to MAX 16–20 total actions (do NOT stop after 1 small task if actions remain).
  * `< $0.60`: Loop consecutive tasks or complete 1 clean sub-step up to MAX 8–10 total actions.
  * MODEL-COST SCALING: Heavy/expensive flagship models stay strictly inside these caps. Low-cost/fast models (e.g., Sol, Mini, Flash) cost far less per action—extend your action cap by ~50% so you do not stop prematurely after 1 small edit.
- PRIORITY, LOOP & SAFE MIDWAY RULES:
  * ALWAYS-LOOP: NEVER stop after 1 task if more `- [ ]` tasks remain and you are below your action cap—move straight to `Task 2`, `Task 3`... in THIS TURN and update `TODO_CONTINUATION.md` ONCE in Step 3 (saves actions).
  * BUDGET OVERRIDES COMPLETION: On hitting your MAX actions (or same error twice), STOP starting new work immediately—even midway. Split remaining work into `- [ ]` sub-tasks, then go to Step 3 -> Step 5 (Case A).
  * SAFE NON-BREAKING MIDWAY: Edit bottom-up (helpers, new components, backend types FIRST; wire into UI/routes/entry points LAST). NEVER pause with dangling imports, half-renamed symbols, or broken callers. If Step 4 fails due to midway stop, fix that wiring or revert only the incomplete hook—app MUST compile and run cleanly.

STEP 3 — FINAL CHECKPOINT BEFORE CHECK:
- Before running any check command, update `TODO_CONTINUATION.md` ONCE: flip all completed tasks/sub-tasks from this turn to `- [x]`, keep/add remaining parts as `- [ ]`, and list created/modified files/functions.

STEP 4 — STRICTLY 1 LIGHTWEIGHT COMMAND ONLY (NO BROWSER OR BUILD LOOPS):
- Run AT MOST 1 command total at the end for fast syntax/type checking (`tsc --noEmit`, `eslint`, or `cargo check`; skip if pure docs).
- FORBIDDEN unless requested: Playwright/browser checks, dev servers, `cargo build`, `--release`, `wasm` builds, full tests, or command loops.
- If a check tool (like Cargo) is missing, do NOT retry—run `git diff --check` once instead and note local/CI verification is needed.

STEP 5 — END OF TURN (CHOOSE EXACTLY ONE):
- CASE A — NOT YET COMPLETED:
  1. Ensure app compiles/runs cleanly and `TODO_CONTINUATION.md` shows `- [x]` done vs `- [ ]` next (with midway stop point and target files).
  2. End immediately with a short **RESUME LOG** (`[PAUSED TO PROTECT CREDITS — READY TO CONTINUE / CREATE PR]`).
- CASE B — ALL TASKS 100% COMPLETED:
  1. Verify every checkbox in `TODO_CONTINUATION.md` is `- [x]`, then DELETE `TODO_CONTINUATION.md` (and any `*_CONTINUATION.md` files).
  2. Write NO continuation steps, state **TASK COMPLETED**, and list manual user steps (review diff, local/CI verify if needed, pull GitHub, PR, deploy).
```

---

## //𝐍𝐞𝐰-𝐓𝐚𝐬𝐤 𝐅𝐨𝐨𝐭𝐞𝐫 (𝐏𝐚𝐬𝐭𝐞 𝐚𝐭 𝐭𝐡𝐞 𝐛𝐨𝐭𝐭𝐨𝐦 𝐨𝐟 𝐀𝐍𝐘 𝐧𝐞𝐰 𝐭𝐚𝐬𝐤 — 𝐒𝐚𝐦𝐞 𝐂𝐡𝐚𝐭 𝐎𝐑 𝐍𝐞𝐰 𝐂𝐡𝐚𝐭):

```text
Budget: $[INSERT AMOUNT] — strictly follow active creditsafe Steps 1–5: create checkbox TODO_CONTINUATION.md first, ALWAYS keep looping consecutive tasks until you reach this budget's action cap or finish all tasks (never stop after 1 small task while actions remain), keep pauses non-breaking, checkpoint - [x] BEFORE running AT MOST 1 fast syntax check (NO browser checks/builds), and end with Case A or Case B.
```

---

## //𝐂𝐡𝐚𝐭 𝐂𝐨𝐧𝐭𝐢𝐧𝐮𝐚𝐭𝐢𝐨𝐧 & 𝐂𝐫𝐚𝐬𝐡 𝐑𝐞𝐜𝐨𝐯𝐞𝐫𝐲 (𝐏𝐚𝐬𝐭𝐞 𝐀𝐋𝐎𝐍𝐄 𝐭𝐨 𝐫𝐞𝐬𝐮𝐦𝐞 𝐢𝐧 𝐒𝐚𝐦𝐞 𝐂𝐡𝐚𝐭, 𝐍𝐞𝐰 𝐂𝐡𝐚𝐭, 𝐎𝐑 𝐍𝐞𝐰 𝐀𝐜𝐜𝐨𝐮𝐧𝐭):

```text
Budget remaining: $[INSERT AMOUNT] — resume strictly per active creditsafe Steps 1–5. Read TODO_CONTINUATION.md (or run git status once if timed out) and max 1–2 files (2–3 if >= $0.80) for the first unchecked - [ ] item, ALWAYS keep looping consecutive tasks until you reach this budget's action cap or finish all tasks (never stop after 1 small task while actions remain), keep pauses non-breaking, checkpoint - [x] BEFORE running AT MOST 1 fast syntax check (NO browser checks/builds), and end with Case A or Case B.
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
