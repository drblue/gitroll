# Gitroll MVP

Gitroll handles class registration for one GitHub organization per installation. The first organization is `the-hive-resistance` and the first class is `YKTYS26H1`.

## Teacher workflow

- Sign in with an approved GitHub account. The approval is tied to the numeric account ID.
- Create and manage classes. Select the existing Classroom 50 student team and add a title and Classroom 50 URL for each assignment.
- Import roster names from CSV, add and remove rows individually, and distinguish any identical display names before sharing the link.
- Open or close registration and replace the class's shared registration link. Students who have already registered can sign in again to resume their flow.
- See roster names, linked GitHub usernames, and registration status. Find accounts already in the student team that lack a roster link and associate them with the correct names.
- Correct or reset an account link. Removing a roster entry, resetting its account link, and revoking access through the student team are separate actions. Manage organization membership and direct access to student repositories in GitHub.

## Student workflow

1. Open the class registration link and sign in with GitHub. Active members of this class's student team go directly to the class's assignment list, including members who joined outside Gitroll.
2. When registration is open, a student with a valid link and no existing registration chooses an available name and confirms it. Claim the roster entry exclusively and allow at most one entry per GitHub account in the class. The shared link grants access to name selection without proving which name belongs to the student.
3. For an existing member of the organization, add the account to the class's student team. Otherwise send a GitHub organization invitation to the numeric account ID and include the class team. If an invitation is already pending, let the student accept it. On **Kontrollera igen**, finish adding the account to the class team if needed.
4. Keep the account link if the invitation fails or expires. The teacher can retry or free a row tied to the wrong account. An account whose class access was explicitly revoked needs the teacher to restore it before it can rejoin through Gitroll.
5. Once active membership in the class team is verified, show the class's assignment links. Classroom 50 handles assignment acceptance and creates the private student repository. Adding another assignment uses the same class membership and account link.

## Pilot before class rollout

Use a GitHub account outside `the-hive-resistance`. Open the shared registration link for `YKTYS26H1`, sign in, choose a test roster name, and accept the GitHub invitation. Verify that the account is active in the correct student team and can accept the Classroom 50 assignment and open the private student repository created from `yktys26h1-inlamningsuppgift-template`. Open the class link again with the same account and verify that a second assignment is available without another name selection.
