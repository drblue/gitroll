# Gitroll MVP: self-service registration for Classroom 50

## Problem Statement

A teacher using Classroom 50 needs to give students access to a GitHub organization and the correct class team before they can accept coursework. Gathering GitHub usernames and inviting students individually takes time. School email invitations depend on each student having verified that exact address on GitHub. A student who has already joined the class should be able to start another assignment without matching their name again.

## Solution

The teacher shares one registration link per class. Students sign in with GitHub, choose their name once, and get an invitation to the organization and class team when needed. A student who is already an active member of that class's student team goes straight to the class's assignment list. Classroom 50 continues to handle assignment acceptance and student repository creation.

## User Stories

1. As a teacher, I want to sign in with my approved GitHub account so that only authorized teachers can manage registration.
2. As a teacher, I want to manage multiple classes under one GitHub organization so that I can use Gitroll across courses.
3. As a teacher, I want to choose the existing Classroom 50 student team for each class so that enrollment grants access to the right coursework.
4. As a teacher, I want to manage titles and Classroom 50 links for several assignments per class so that students can find current coursework in one place.
5. As a teacher, I want to import a CSV of student names so that I can prepare a class roster without collecting GitHub usernames.
6. As a teacher, I want to add and remove roster entries individually so that I can handle changes after import.
7. As a teacher, I want to distinguish roster entries with identical names so that students can choose the intended entry.
8. As a teacher, I want one hard-to-guess registration link per class so that I can share the same link with the entire group.
9. As a teacher, I want to close registration and replace the link so that I control when and through which link new students can register.
10. As a teacher, I want to see names, linked GitHub usernames, and registration status so that I can tell who still needs help.
11. As a teacher, I want to see active student team members without roster links so that I can connect accounts that joined outside Gitroll.
12. As a teacher, I want to link an already enrolled account to the correct roster entry so that the student can keep working while I update the roster.
13. As a teacher, I want to correct or reset a wrong account link so that the intended student can register under their name.
14. As a teacher, I want to retry a failed or expired invitation so that the student can continue with the same account link.
15. As a teacher, I want to remove a roster entry independently of revoking team access so that a list correction does not affect GitHub permissions.
16. As a teacher, I want to revoke access to one class so that the account cannot rejoin through Gitroll without my approval.
17. As a teacher, I want revoking one class to preserve invitations other classes rely on so that students can still join those classes.
18. As a student, I want to use my existing GitHub account so that I can register even when my school email is unverified on GitHub.
19. As a student, I want to see only available names after signing in through a valid class link so that I can choose my roster entry.
20. As a student, I want my confirmed name selection to stay linked to my account so that another account cannot claim the same entry.
21. As a student outside the organization, I want Gitroll to request the GitHub invitation for me so that I can join the correct student team by accepting it.
22. As a student who already belongs to the organization through another class, I want to join the new class team after choosing my name so that I can proceed without a second organization invitation.
23. As a student with an existing pending invitation, I want to accept it and check again so that Gitroll can finish my enrollment.
24. As a returning student, I want to resume my registration after signing in again, even if registration has since closed or the shared link changed.
25. As a student already in the class team, I want to skip name selection so that I can go straight to the assignments.
26. As a student, I want my account link to apply to every assignment in the class so that new coursework never requires another name match.
27. As a student, I want to open the assignment in Classroom 50 so that I can accept it and get my private student repository.

## Implementation Decisions

- Deliver the first version as a locally runnable TypeScript application using SQLite. Configure one GitHub organization per installation. Each class has its own roster, student team, registration link, and teacher-managed assignment list.
- Use the numeric GitHub account ID for account links and the teacher allowlist. Display the GitHub username for recognition. A class has at most one roster entry per GitHub account, and a roster entry can have at most one linked account. Claim it atomically after confirmation.
- Check active membership in the class's Classroom 50 student team after GitHub sign-in. Membership lets a student proceed to assignments even without a Gitroll account link. A pending invite or organization membership alone does not establish class membership. Preserve any existing account link across assignments, failed invitations, and return visits. [Classroom 50 enrollment model](https://github.com/foundation50/classroom50/wiki/How-Classroom-50-Works#from-invitation-to-enrollment)
- Require an open registration and current shared link to make a new claim. Return visits for existing registrations are authenticated by GitHub. The shared link gives access to name selection and does not verify the student's identity against the roster.
- Use a GitHub App installation with organization Members read and write permission. Invite accounts outside the organization by numeric ID with the ordinary member role and the target team. For active organization members, add the account to the class team when needed. Reuse a pending organization invitation. If it lacks the class team, **Kontrollera igen** verifies acceptance and finishes enrollment for the existing account link. An uncertain or failed GitHub operation must not be reported as completed. [GitHub organization invitations](https://docs.github.com/en/rest/orgs/members#create-an-organization-invitation) · [GitHub team membership](https://docs.github.com/en/rest/teams/members#add-or-update-team-membership-for-a-user)
- Keep account-link correction, roster removal, and class access revocation independent. A class access revocation applies to the account in that class and requires explicit teacher restoration before Gitroll can grant access again.
- When revoking class access before acceptance, cancel an organization invitation only if Gitroll created it for that class and no other class depends on it. If another class depends on it, leave it in place and direct the teacher to remove the affected class team in GitHub if the team is attached. Verify the current state in case the student has already accepted. For active members, remove the account from the affected class team. Keep the teacher informed of any GitHub action still outstanding. [GitHub invitation management](https://docs.github.com/en/organizations/managing-membership-in-your-organization/canceling-or-editing-an-invitation-to-join-your-organization)
- Show the student's assignment links only after verifying active class membership. Classroom 50 owns assignment acceptance and creates the student repository from the existing template.

## Testing Decisions

- Use the application's HTTP boundary as the primary test seam for teacher and student journeys. Run with temporary SQLite storage and controlled GitHub responses. Assert visible results and externally observable side effects rather than private functions or SQL statements. The repository currently contains design documents and has no existing tests to build on.
- Cover teacher authorization and class isolation, a roster import, students choosing the same row concurrently, one account attempting multiple claims in the same class, and closed or rotated registration links. Verify that a returning linked student resumes without picking a new name.
- Cover invitation creation, an already pending invitation for another class, an existing organization member joining a new team, a verified member bypassing name selection with no account link, and a failed or expired invitation keeping the link for retry.
- Cover correction and removal as independent from revocation, cancellation of an invitation exclusive to one class, preservation of a shared invitation, and blocking self-registration after explicit revocation until a teacher restores access.
- Verify the application in a browser. Before sharing the registration link with the class, run the real pilot below. Simulated GitHub responses do not establish that the organization's configuration works end to end.

## Out of Scope

- School email verification or one-time codes.
- Supporting multiple GitHub organizations in a single installation.
- Managing organization membership removal or direct access to existing student repositories.
- Creating or editing Classroom 50 assignments, writing its roster file, or provisioning student repositories from Gitroll.
- Selecting or setting up production hosting.

## Further Notes

The first pilot uses `the-hive-resistance`, class `YKTYS26H1`, and the private template `yktys26h1-inlamningsuppgift-template`. Use an existing GitHub account outside the organization and a test roster entry. Open the shared link, sign in, select the entry, accept the GitHub invitation, and verify active membership in the correct student team. Open the assignment in Classroom 50, accept it, and verify that the account can open the private repository created from the template. Finally, return to the class link and open a second assignment without selecting a name again.
