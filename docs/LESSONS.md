# Lessons from running IPAM in production

These are real problems hit while building and operating IPAM. They are written up so other builders of small internal tools can avoid them.

## 1. A free-tier read quota is a production risk

On a managed document database, a free daily read quota ran out and every user saw allocation errors. Fixes that helped: precomputed summaries on parent documents, capped and ordered feeds, lazy-loaded tabs, and cursor-based notifications. The real fix was moving to a self-hosted backend where reads are not metered.

## 2. Cold-start authentication race

On first load, the first realtime query could run before the auth token was attached, hit "permission denied", and the stream provider latched that error, so every page showed an error until Retry. Fix: wrap every stream in a retry policy that waits for the auth state, and use `await for` instead of `yield*` so errors are catchable.

## 3. Unset number fields read back as zero

PocketBase returns `0` for an unset number field, not `null`. A Dart `value ?? default` therefore never applied the default, and a shift with duration `0` expired immediately: nobody appeared on duty and the check-in button never flipped. Fix: coerce both `null` and non-positive values to the default at the model boundary.

## 4. Double-submit through stream lag

A button that depends on a realtime stream can be tapped twice before the stream updates. Fix: lock the action locally until the server response arrives instead of waiting for the stream.

## 5. Migration silence is not success

PocketBase applies JavaScript migrations without logging anything, so a quiet log does not prove success. Always verify the schema directly, because the server silently drops unknown fields, and confirm the field exists before shipping a client that uses it.

## 6. Web back button

Classic Navigator 1.0 does not sync with browser history, so the back button closed the whole app. Migrating to go_router with hash URLs fixed it properly.

## 7. Verify a migration with numbers

The cloud-to-self-hosted migration followed a strict order: freeze writes, export, wipe, import, then verify with a comparison that reported zero mismatches before reopening. A rollback window was kept until confidence was established.

## 8. Enforce rules on the server

Early on, some checks lived only in the client. Moving overlap protection and branch isolation into server-side hooks and collection rules made them atomic and impossible to bypass.

## 9. Right-to-left is a feature, not a flag

Mixed Arabic and English text needs per-segment direction handling in documents and PDFs. Test with real mixed content, not only pure Arabic.
