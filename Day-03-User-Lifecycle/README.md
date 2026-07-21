# Day 3 – User Lifecycle Management

## Objective

Learn how Microsoft Entra ID manages user accounts throughout their lifecycle.

## Tasks Completed

- Created a new user
- Disabled the account
- Re-enabled the account
- Deleted the account
- Restored the deleted account

## Skills Practiced

- User administration
- Identity lifecycle management
- Soft deletion
- Account recovery

## Key Learnings

- Disabled accounts remain in the tenant but cannot sign in.
- Deleted users are recoverable for 30 days.
- Restoring a deleted user preserves important account information.
- Deleting an account permanently should be done only after organizational policies are followed.

## Interview Questions

### Why disable instead of delete?

Disabling preserves the account and its resources while preventing sign-in. This is useful for employees on leave or when an investigation is in progress.

### How long can a deleted user be restored?

30 days.

### What happens after 30 days?

The user is permanently deleted and cannot be restored.
