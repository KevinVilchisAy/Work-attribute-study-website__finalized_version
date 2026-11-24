# Desirable Attributes at Work

A browser-based psychology study that measures how quickly participants choose which of two personality attributes is more desirable in an applicant for a Customer Service Representative position. The website presents the task with jsPsych and sends completed study data to Qualtrics.

## Participant flow

1. Review the consent form, enter a SONA ID, and read the task instructions and job description. The site also accepts a SONA ID through the `sona_id` URL parameter.
2. Complete the practice trials, then press **Space** to start the main task.
3. On each trial, use **S** to select the word on the left or **K** to select the word on the right. The site randomizes the main trial order and the left/right placement of words within each pair.
4. Complete the follow-up and demographic questions. After the thank-you screen, the browser redirects to Qualtrics automatically.

## Project files

| File | Purpose |
| --- | --- |
| `index.html` | Main study page, including consent, instructions, trials, questionnaires, and the Qualtrics redirect |
| `practice_pairs.txt` | JSON word-pair bank for the practice trials |
| `actual_test_pairs.txt` | JSON word-pair bank for the main task |
| `qualtrics-receiver-rt-id-word.js` | Qualtrics question JavaScript that validates and saves the website payload |

## Run locally

The website uses HTML, CSS, JavaScript, and jsPsych 8.2.2. It loads jsPsych and its plugins from unpkg, so the browser needs internet access. Python is only used here to serve the files locally.

1. Put both word-pair files in the same directory as `index.html`. Obtain the study's `practice_pairs.txt` before running the site. It is missing from the project files reviewed for this README.
2. Open a terminal in that directory and run:

   ```bash
   python3 -m http.server 8000
   ```

3. Open [localhost:8000](http://localhost:8000/) in a browser with a physical keyboard. Use the HTTP address so the page can load both word banks.

## Edit the word banks

Both `.txt` files contain JSON arrays. Each pair needs a unique `pair_id` within its file, two distinct words (`word1` and `word2`), and a `condition`. Use `practice` for practice pairs and `matched_positive`, `matched_negative`, or `mixed` for main-task pairs. For a mixed pair, set `more_desirable` to the exact text of one of its two words. The site checks both files before starting the study.

## Qualtrics handoff

Set `return_url` in `index.html` to the receiving survey. Add the contents of `qualtrics-receiver-rt-id-word.js` to the receiving question's JavaScript and match the Survey Flow embedded-data names to the receiver's fields, including `__js_sona_id`, `__js_trials_json`, and `__js_careless`. Use a test survey and a test SONA ID for end-to-end verification.

When a participant finishes, the site sends the SONA ID, follow-up and demographic answers, and main-task trials in presentation order. For each trial, `rt` is the time in milliseconds from word-pair appearance to the keypress, `id` is the original pair ID, and `word` is the selected word. The payload includes main-task trials only. The receiver stores the trial array in `__js_trials_json` and advances the survey.

The separate Qualtrics cleaning pipeline can turn a numeric-value CSV export into one row per valid main-task trial and group the rows by the answer to the careless-response question.
