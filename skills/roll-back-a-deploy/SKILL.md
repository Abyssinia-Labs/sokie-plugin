---
name: roll-back-a-deploy
description: Put a Sokie server back on an earlier deploy. Use when someone asks to roll back, undo a deploy, or restore the last version that worked.
---

## Workflow

1. Call `listServers` and confirm which server they mean.
2. Call `listDeploys` and find the deploy to go back to: the one they name, or the last `live` or `superseded` deploy before things broke.
3. Call `checkRestore` for that deploy and show what restoring it would change, or why it can't be restored.
4. Ask the person to confirm, naming the server and the deploy's number.
5. Call `restoreDeploy`. Sokie may ask the person to allow it, in the client or through a link; wait for their answer, then make the same call again if it asks you to.
6. Follow the new deploy with `getDeploy` until it's `live` or `failed`, and say which.

## Limits

- Never restore without a clear yes.
- On a server linked to a GitHub repository, a restore stops production following the default branch until someone resumes it. Say so before restoring.
