---
name: find-failing-calls
description: Find out why a Sokie server's calls are failing, and say what to do next. Use when someone says a server's tools are erroring, its calls are failing or being refused, or the server looks broken.
---

## Workflow

1. Call `listServers` and confirm which server they mean. If the key reaches only one, use it.
2. Call `listLogs` for that server with `errors: true`. Note which tools fail, each call's `outcome`, `errorClass` and `upstreamStatus`, and when the failures began.
3. Call `listDeploys` and check whether the failures began with a deploy.
4. If they did, call `getDeployByNumber` for that deploy and say what changed.
5. Say what the outcomes point to:
   - `toolError` with `errorClass` `UPSTREAM`: the API behind the server failed, with the status in `upstreamStatus`. A 401 or 403 usually means its credentials need setting again.
   - `toolError` with `BINDING_MISUSE`: the agent sent arguments the tool doesn't take.
   - `toolError` with `POLICY_DENIED`: something has to change first, such as a caller's own key.
   - `refused`: the call had no token, or one Sokie didn't accept, so the client needs to sign in again.
   - `paused`: the server is paused.
   - `rateLimited`: the caller went over its limit and was told when to try again.
   - `failed`: the server couldn't answer at all. Check whether its latest deploy failed.

## Limits

- Only read. Change nothing.
- Logs keep what happened, never what a call sent or returned, so don't ask for arguments.
- If nothing is failing, say so and stop.
- If a deploy caused it, offer the `roll-back-a-deploy` skill; don't restore without being asked.
