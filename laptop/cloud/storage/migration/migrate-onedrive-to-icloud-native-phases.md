# Task: Phased OneDrive migration with native iCloud uploads

Migrate the approved OneDrive files through a local archive into iCloud Drive. Use rclone to
download from OneDrive and Apple's built-in iCloud Drive synchronisation to upload. Inventory
first, test a representative pilot, then process bounded batches with overlapping downloads and
uploads. Preserve the source and report progress until the work finishes or a concrete blocker
prevents it.

This is a reusable execution template. Editing or reviewing it does not authorize migration.
When explicitly invoked for execution, preparation, the pilot, and subsequent batches are
authorized within the settings and safeguards below. Do not request repeated routine approval.
Authentication, unresolved conflicts, destructive actions, and material scope changes may still
require user participation. Respect operating-system and tool permission boundaries.

## Replaceable settings

Resolve these settings before execution and save the resolved values in the private workspace.
Angle-bracket values are placeholders, not literal paths or shell commands. Resolve paths before
passing them to a tool. Never evaluate supplied paths as shell code. Keep resolved paths, account
identities, file inventories, and run reports in the private workspace, never in this public
template or repository. Commit only generic placeholders and reusable instructions.

| Setting | Value or default |
| --- | --- |
| `SOURCE_REMOTE` | `onedrive` — dedicated rclone connection name |
| `SOURCE_SUBPATH` | Empty for the account root, or `<relative source folder>` |
| `ICLOUD_ROOT` | `<actual local iCloud Drive root, confirmed on this Mac>` |
| `DESTINATION_SUBDIR` | `<new migration folder inside iCloud Drive>` |
| `WORKSPACE` | `<absolute private local workspace path>` |
| `CONFIG_PATH` | `<WORKSPACE>/rclone.conf` |
| `ARCHIVE_ROOT` | `<WORKSPACE>/archive` |
| `REPORT_DIR` | `<WORKSPACE>/reports` |
| `EXCLUDED_PATHS` | `Personal Vault` by default; list additional approved exclusions |
| `BATCH_TARGET_BYTES` | `3000000000` to `5000000000` |
| `BATCH_MAX_BYTES` | `5000000000`; record exceptions for indivisible larger items |
| `MAX_DOWNLOAD_BATCHES` | `2` concurrently after the pilot |
| `MAX_LOCAL_COPY_BATCHES` | `1` into iCloud Drive at a time |
| `TOTAL_DOWNLOAD_TRANSFERS` | `8` initially; tune from observed performance |
| `TOTAL_DOWNLOAD_CHECKERS` | `8` initially; independent of transfer count |
| `LARGE_FILE_STREAMS` | `4` when supported and useful; benchmark before increasing |
| `RETRIES` | `5` |
| `PROGRESS_INTERVAL_SECONDS` | `600` maximum between user reports |
| `STALL_INTERVAL_SECONDS` | `600` without observable work before investigation |
| `ICLOUD_RESERVE_BYTES` | `5000000000`; use a lower value only by explicit decision |
| `LOCAL_RESERVE_BYTES` | `10000000000`; revise from the measured disk budget |
| `VERIFICATION_MODE` | `size-and-upload-status` by default; optional `full-content` |
| `ALLOW_DESTINATION_RENAMES` | `false`; enable only with an agreed naming policy |
| `RETAIN_ARCHIVE` | `true`; later deletion requires explicit authorization |
| `EXTERNAL_REVIEW_COMMAND` | Optional approved local reviewer, otherwise unset |

A batch is a bounded set of source paths. A manifest is a saved list of expected paths, types,
exact byte sizes, and destination names. A package is a directory that macOS presents as one
document. A GB is 1,000,000,000 bytes; a GiB is 1,073,741,824 bytes. Always calculate capacity
using exact bytes and label displayed units.

## Safety, credentials, and permissions

- Use `rclone copy`, never `sync` or `move`. Never delete or alter source content or sharing.
  Never delete unrelated destination entries or overwrite conflicting existing files.
- Do not change subscriptions, unlink accounts, uninstall applications, or alter user shortcuts as
  part of migration. Those are separate tasks requiring explicit instructions.
- Do not configure an rclone iCloud connection or supply Apple credentials to rclone. Use the Mac's
  existing Apple account and native iCloud Drive service. The native OneDrive app is not required.
- Keep the workspace outside repositories and every cloud-synchronised directory. Desktop and
  Documents may be synchronised. Verify actual locations, existing links, and filesystem access;
  do not rely on a directory's name. Restrict workspace access to the user account.
- Encrypt the dedicated rclone configuration before adding credentials. Preserve unrelated
  configurations. Prefer Microsoft read-only permissions where supported and verify the requested
  permissions during sign-in. Keep the configuration writable for normal token refresh.
- Have the user enter sign-in credentials, approval codes, and the configuration password privately.
  Never print configuration contents, tokens, password files, browser cookies, or process
  environments. Never put secrets in command arguments, source files, reports, or repositories.
- For unattended child commands, a private runner may read the configuration password without echo,
  hold it in memory, and supply `RCLONE_CONFIG_PASS` only to rclone child processes. This
  environment variable unlocks encrypted configurations; it does not protect against equally
  privileged local processes. Disable shell tracing and clear the runner's reference on exit.
  Require private re-entry after restart. Do not create a plaintext password file for convenience.
- Pause account access on authentication failure. Reauthenticate privately, verify account identity,
  and resume the ledger. Authentication errors do not mean missing or corrupted files. Do not
  weaken account security settings to repair authentication.
- Session approval does not authorize persistent unrestricted access. Never change global agent
  approval or filesystem restrictions to suppress prompts. Do not enable browser scripting,
  Accessibility control, or extract an existing browser session for this workflow.
- Do not modify private sync databases, kill Apple sync services, or use undocumented sync commands.
  Keep a record of any explicitly approved temporary setting change and its original value.
- Keep the Mac powered and awake with `caffeinate -i` for the entire active job, including native
  uploads. Track and stop only processes started for this migration. Closing the lid may suspend
  work; explain that the Mac must remain awake and connected.

## 1. Preparation and source discovery

1. Check macOS, rclone, available storage, network connectivity, and the required commands.
   Install rclone through Homebrew if absent. Verify relevant options against the installed version.
   Confirm the source account, native iCloud account, resolved destination, and private workspace.
2. Inventory source folders, ordinary files, exact bytes, and available modification information.
   Avoid `--fast-list` during initial discovery. Retain listing errors and exit statuses. An
   incomplete listing must never be reported as an empty directory or a complete inventory.
3. Apply explicit exclusions during listing and copying. Do not repeatedly attempt Personal Vault
   when excluded. Record that its contents were neither inspected nor migrated. Reconcile root
   folders and special-item categories with provider information where practical; ordinary rclone
   listing alone does not establish complete account coverage.
4. Classify known content as transferable, separately exported, excluded, or unresolved. Investigate
   OneNote notebooks, shared items, shortcuts, Live Photos, blocked downloads, and unknown sizes.
   Exposing notebook metadata does not make notebooks ordinary downloadable files. Copying a still
   image does not establish preservation of Live Photo motion. Never bypass malware restrictions.
5. Detect unsupported names, case or Unicode collisions, links, large files, packages, and empty
   folders. Do not follow links outside scope. Keep packages intact when assigning batches. Record
   every expected file once, plus folders separately, including empty ones.
6. Inspect existing archive and destination entries. Record conflicts and extras. Under size mode,
   matching path and size mean only a size match; differing bytes of the same length can go
   undetected. Use a new destination folder where possible. Do not silently adopt unrelated
   pre-existing files as migrated content; resolve their reuse explicitly.
7. Record a source manifest before copying and refresh it at completion. Ask the user to avoid
   source edits; report observed changes. Verification describes the final observed state, not an
   atomic snapshot or proof that no intermediate changes occurred.

Read-only preparation means no cloud-content changes. Private reports and normal configuration
writes for authentication refresh are permitted. Do not infer transferable size from account usage.

## 2. Capacity, batches, and pilot

- Show a batch table: paths, files, folders, bytes, largest item, estimated destination growth,
  peak local usage, special handling, and status. Keep directories together when possible; split
  large branches recursively or use exact file lists. Preserve each original relative path.
- Split by file count and operational complexity as well as bytes. Use lossless file selection
  supported by the installed rclone. Lists containing nonexistent files may not produce errors;
  reconcile every expected path independently. Track empty directories outside file lists.
- Measure iCloud free space using a supported available account view. Local disk free space is not
  iCloud capacity. If exact measurement is unavailable, label the precision and uncertainty rather
  than inventing a byte value. Avoid repeatedly demanding the same user check when a current
  measurement and conservative remaining-work budget suffice.
- Before uploads, require remaining cloud capacity to cover remaining new data, the configured
  reserve, and explained overhead or concurrent account activity. Never proceed through a shortage.
  Phasing reduces queued work, not final cloud storage requirements.
- Budget local storage for the cumulative archive, iCloud local copies, partial files, and optional
  verification downloads. Do not assume caches disappear. Before starting each batch, account for
  space already reserved by other active batches. Stop scheduling when the local reserve is at risk.
- Start a small representative pilot using real files and final destination paths. Include nested
  folders, small and large files, relevant names, packages, and empty folders where present. Test
  download, selected verification, local staging, native upload observation, and final reporting.
- Repeat identical pilot copy operations to test safe reuse. Investigate metadata conflicts rather
  than automatically replacing files. Preserve content checks if full-content mode was selected.
  A completed-copy rerun does not prove recovery from every possible interrupted transfer.
- Pass the pilot only when expected paths and sizes reconcile, required folders exist, Apple reports
  uploads complete, and no unresolved temporary or unintended new entries remain. In full-content
  mode, also demonstrate the complete independent verification workflow and its practical workload.
- Publish pilot results, then continue automatically within the chosen settings. Stop only for an
  actual failed gate, conflicting content, capacity shortage, or unresolved decision.

## 3. Run bounded downloads and uploads together

1. Use one scheduler and a durable ledger, meaning saved state that survives interruption. Prevent
   duplicate workers from claiming the same batch. Save updates safely so a crash cannot leave a
   half-written state file. Track worker process identifiers and log paths.
2. After the pilot, keep up to `MAX_DOWNLOAD_BATCHES` downloading distinct source paths. When one
   finishes, validate it and queue its local iCloud copy immediately. Start the next eligible
   download when a slot and capacity are available; do not wait for an unrelated upload to finish.
3. Divide `TOTAL_DOWNLOAD_TRANSFERS` and `TOTAL_DOWNLOAD_CHECKERS` across active rclone processes;
   do not give each process the entire global allowance. `--transfers` controls simultaneous file
   transfers, `--checkers` controls file comparisons, and `--multi-thread-streams` controls parallel
   parts of eligible large files. These are not CPU-core allocations. Tune from measured throughput,
   storage pressure, and provider throttling, rather than processor count alone. Back off on errors.
4. Download the exact batch using `rclone copy --immutable`, the dedicated encrypted configuration,
   retries, progress statistics, and a separate INFO log. Preserve relative paths. Validate
   arguments as structured values rather than shell text. Save the exact options, file list, and
   exit status.
   Retrying must reuse completed matches without duplicating or overwriting conflicting files.
5. Reconcile expected paths, sizes, counts, missing files, and errors before staging. In default
   mode, use `rclone check --size-only` where useful, plus explicit manifest coverage. Do not hash
   or redownload the dataset in this mode. Preserve empty directories explicitly and verify them.
6. Copy validated batches from the archive into the confirmed local iCloud destination. Serialize
   this local staging with `MAX_LOCAL_COPY_BATCHES`; Apple's service controls actual cloud uploads.
   A local-to-local `rclone copy --immutable` is acceptable, with both paths verified as local.
   Never use an `icloud:` destination. Restrict the operation to the exact batch and approved names.
7. Rclone may expose temporary `.partial` names before renaming. Observe pilot behavior and record
   leftover temporary entries. Do not automatically add `--inplace` to avoid them: it exposes an
   incomplete file under its final name. Do not delete cloud-visible leftovers without approval.
8. Monitor native uploads while download slots continue working. Keep capacity reservations and
   pending-upload totals current. Reduce scheduling when Apple falls behind or storage is tight.
   Report why any slot is intentionally idle; do not leave eligible batches unscheduled silently.
9. Keep immutable archive contents until completion or explicit cleanup authorization. Modification
   times and other metadata are best effort unless the user makes them mandatory. A restart policy
   based only on size must be stated explicitly and cannot establish byte equality.

## 4. Native Apple upload evidence and verification

### Default: size and upload status

- Reconcile each mapped source path with archive and destination file sizes. Compare file and
  folder counts against the approved manifest, and list extras separately. Equal totals alone do
  not prove that the expected individual paths are present.
- Use documented native Apple file-resource information where available. For example, Foundation,
  Apple's application support framework, exposes `ubiquitousItemIsUploadedKey` to report whether
  an iCloud item has uploaded. Record upload state, errors, observation time, and evidence method.
  A successful local copy or matching local size alone is not upload confirmation.
- Some package children may have no individual upload value. Treat an absent value as unknown,
  never automatically true. Where validated on this Mac, record completion through the enclosing
  package's explicit uploaded state, with a mapping of covered children. Never override a child's
  explicit false value or error with a parent's success, and never use the overall root as blanket
  evidence for ordinary files. Confirm expected package contents and flag duplicate variants.
- Use Finder's iCloud Status as a supported fallback. Pending, ineligible, out-of-space, or error
  states are unresolved. Report unavailable evidence honestly. Do not claim Apple's upload status
  is an independent downloaded-content comparison.
- Report completion as **Complete for approved scope — paths and sizes checked; native upload
  status confirmed**. State that byte equality was not independently verified. Do not perform full
  content validation merely to satisfy an older draft when size mode was selected.

### Optional: full content

- Enable only when explicitly selected. Compare source and archive using `rclone check --download`
  and record exit statuses and coverage. Compute SHA-256 hashes, fingerprints of complete file
  contents, for archive and independently retrieved iCloud copies.
- Prove the retrieval is fresh and cloud-sourced. An existing local cache, matching local hashes,
  or a same-device request to download does not by itself prove independent cloud retrieval.
- Demonstrate practical verification of nested paths and high file counts during the pilot. Use
  supported retrieval methods; do not extract browser sessions or automate repeated Save dialogs.
  If independent retrieval is unavailable, report this mode incomplete instead of silently reducing
  coverage. No sampling may be called full content verification.

### Exceptions and stalled uploads

- Unsupported destination names may be changed only under an approved naming policy or specific
  user decision. Keep the source and archive names unchanged. Store the source-to-destination
  mapping, choose distinct collision-free names, and verify the mapped path and size. Do not
  convert to ZIP or another representation when the user authorized only renaming.
- Diagnose stalled uploads through supported status observations. Do not create duplicate package
  copies on every retry. Destination-only metadata changes to encourage a retry require an explicit
  decision and recorded before/after values; never change source bytes or quietly alter timestamps.
- If status is unknown, errors persist, or an item is ineligible, identify the exact paths and
  continue unrelated safe work where possible. Do not repeatedly request browser login or change
  browser security settings to bypass the limitation.

## 5. Progress, restart, and final report

- Show progress at least every `PROGRESS_INTERVAL_SECONDS` while actively supervising, and at
  download completion, upload staging, upload completion, retry, stall, and failure. Keep a live
  progress file as well. A background worker must not be described as providing future chat
  updates unless a supported notification mechanism is actually running.
- Report per batch: state, completed/expected files, completed/expected bytes, current speed,
  elapsed time, pending uploads, latest activity, and next action. Distinguish download, local
  staging, Apple upload confirmation, and verification. Do not infer uploaded bytes from staged
  bytes. Label file percentage separately from byte percentage; changing the denominator or
  restarting counters requires an explanation. Use a stable manifest and unique completion counts.
- Investigate no observable work for `STALL_INTERVAL_SECONDS`. Separate slow active transfers,
  retries, authentication failure, and Apple upload delay. Show the blocker and recovery action.
- On restart, inspect actual processes and saved state before starting replacements. Reconcile
  partial files, source changes, conflicts, and storage reservations. Resume unfinished batches;
  never reset a successful batch or create a second competing scheduler unnecessarily.
- If `EXTERNAL_REVIEW_COMMAND` is set, obtain a fresh read-only review of the runner and concurrency
  plan before bulk execution, and after material changes. Give the reviewer sanitized code and
  evidence, never credentials. Apply supported findings; document disagreements. Do not substitute
  an earlier review for a newly requested one or claim a review that never ran.
- Finish with a refreshed source manifest and a complete destination-path reconciliation. Record
  known omissions and approved exclusions separately from migrated files. Completion requires
  every in-scope item to satisfy the selected verification mode, required folders to exist, and no
  unresolved required upload or conflict. Otherwise report **Partial**, or **Blocked before
  migration** if nothing was copied.
- Produce human-readable and structured reports: settings, versions, scope, exclusions, name
  mappings, batches, counts, exact unique bytes, observed durations, checks and exit statuses,
  native upload coverage, errors, extras, remaining storage and measurement times, destination,
  and artifact locations. Logical file bytes are not measured network traffic. Do not fabricate
  precise Apple transfer totals or claim complete original-account coverage from listed files alone.

## 6. Completion and cleanup

- Stop only migration-owned workers, observers, and sleep-prevention processes after uploads and
  final reporting finish. Verify no migration job remains active before cleanup.
- Retain source, archive, encrypted configurations, and evidence by default. Summarize what remains
  and why. Ask for a cleanup decision only when needed; prior cleanup authorization applies.
- Before deleting a workspace, preserve requested reports or reusable template updates outside it.
  Inspect the exact target and links so removal cannot reach either cloud or unrelated data. Explain
  which archives, configurations, credentials, and reports will be lost. Delete only authorized
  paths and verify their absence. Updating this template alone does not authorize deletion.
- Retire migration credentials or revoke account access only with explicit authorization. Do not
  claim deleting a local configuration revokes an already issued token. Remove any user-authorized
  temporary password file without displaying its contents.
- Restore only temporary permissions and settings changed for this job, to recorded original
  values. Do not alter unrelated permissions. Report manual restoration still required; do not
  re-enable broad control solely to disable another setting. Never claim cleanup succeeded without
  checking it or clearly attributing user confirmation.

## References to check for the installed versions

These describe mechanisms, not evidence that a particular migration succeeded. Read current
primary documentation for supported behavior and relevant firsthand reports for actual failures
when troubleshooting. Distinguish observed results, documentation, and inference.

- [Rclone OneDrive limitations](https://rclone.org/onedrive/)
- [Rclone copy and empty folders](https://rclone.org/commands/rclone_copy/)
- [Rclone exact file selection](https://rclone.org/filtering/)
- [Rclone size and content checks](https://rclone.org/commands/rclone_check/)
- [Rclone settings and concurrency](https://rclone.org/docs/)
- [Rclone configuration encryption](https://rclone.org/docs/#configuration-encryption)
- [Apple Finder iCloud status](https://support.apple.com/guide/mac-help/mchlc994344b/mac)
- [Apple file resources](https://developer.apple.com/documentation/foundation/urlresourcekey)
- [Apple Desktop and Documents sync](https://support.apple.com/109344)

Begin with preparation, resolve the settings, inventory directories and sizes, show the plan,
execute the pilot, and continue through the remaining batches when its checks pass.
