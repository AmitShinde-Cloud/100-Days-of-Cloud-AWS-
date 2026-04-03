Cloud (AWS) Day 04: Enable Versioning for S3 Bucket
This document describes the fourth step in the AWS migration workflow: enabling S3 bucket versioning for controlled data protection. Versioning ensures that objects stored in a bucket are not permanently lost when overwritten or deleted. Instead of replacing or removing data directly, S3 keeps historical versions, allowing rollback and recovery.

Objective
Enable versioning on the S3 bucket:

Bucket Name: devops-s3-4951
Region: us-east-1 (N. Virginia)
Action: Enable Versioning

Why Versioning Matters
By default, when you upload a file with the same name as an existing file in S3, the older file is replaced. If you delete a file, it is permanently removed.

When versioning is enabled:

Every uploaded object receives a unique Version ID.
Uploading the same file name creates a new version instead of replacing the old one.
Deleting an object inserts a delete marker instead of removing data permanently.
Previous versions remain stored and can be restored.
This mechanism protects against:

Accidental overwrites
Accidental deletions
Application deployment mistakes
Data corruption scenarios
Versioning transforms S3 from simple storage into a recoverable storage system.

Step 1: Login and Select Region
Log in to the AWS Management Console.
Confirm the region is set to N. Virginia (us-east-1).
Search for S3 in the Services search bar.
Step 2: Open Target Bucket
Locate the bucket named devops-s3-4951.
Click the bucket name to open its dashboard.
Step 3: Enable Versioning
Open the Properties tab.
Locate the Bucket Versioning section.
Click Edit.
Select Enable.
Click Save changes.
Step 4: Confirmation
A success message confirms the update.
Versioning status changes from Disabled to Enabled.
At this point, all future uploads will receive unique Version IDs.
