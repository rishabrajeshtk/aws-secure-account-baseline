#### AWS Secure Account Baseline

A Hands-on project where I set up and tested simple security controls (public-access blocking, encryption, versioning, least-privilege IAM, and audit logging. Every control used has been shown with a screenshot as evidence, along with the **limitations,** which include any of the tasks that I wasn't able to perform.



###### Account type: AWS Free plan 

###### Services used: Amazon S3, AWS IAM, AWS CloudTrail, AWS CloudShell

###### Region: ap-southeast-2 (account was default; couldn't change region in the provided console layout)



###### Controls and risks it reduces:



* Block Public Access on the bucket - Accidentally exposing data to the internet
* Versioning enabled - Accidental overwrite or deletion of objects
* Default Encryption - Stored data would be readable if the storage is compromised 
* ACLs disabled (bucket owner enforced) - Hidden per-object permissions that bypass bucket policy 
* Least-privilege IAM user - Users having more access than they need; demo-reader has a read-only policy for this one bucket.
* CloudTrail (multi-Region trail, management events, separate log bucket) - logs the record of who changed what



###### Evidence:



01-s3-versioning.png	Versioning enabled

02-s3-encryption.png	Default encryption: SSE-S3

03-s3-block-public-access.png	Block Public Access on

04-s3-public-policy-blocked.png	Public bucket policy rejected

05-iam-readonly-policy.png	Read-only policy for demo-reader

06-s3-upload-denied.png	     CLI test: download works, upload denied

07-cloudtrail-trail.png	     CloudTrail trail settings

08a-cloudtrail-event-history.png, 

08b-cloudtrail-event-history.png	Event history: the CreateBucket event and the denied PutBucketPolicy event

09-cloudtrail-log-files.png	    Log files delivered to the log bucket



###### How I tested least privilege



AWS's new project-based console does not let IAM users have console access, so I tested from the command line instead:



1. Created demo-reader with no permissions, then attached an inline policy allowing only s3:ListBucket and s3:GetObject on this bucket.
2. Created a temporary access key and used it in CloudShell, checking with aws sts get-caller-identity that I was acting as demo-reader.
3. Downloaded hello.txt which worked , then tried to upload a file (denied).
4. Deleted the access key



###### Limitations:



* GuardDuty was not tested. The console reported that access to it is not supported in this account. I believe the Free plan is the reason but I have not confirmed it. I did not upgrade the plan, to keep the credit-based protection against unexpected bills.
* MFA is not claimed as a verified control. I registered a passkey as MFA on my AWS Builder ID, and a sign-in from a fresh private window showed a passkey prompt that I completed with my phone, but I have no screenshot of that prompt. 
* Region. Everything is in the account default region, ap-southeast-2. I could not find a way to change region in my console layout, and I did not enable any opt-in regions.
* CloudTrail data events are not enabled (they cost extra), so object-level actions such as the denied upload are not in the logs. Management events only.
* MFA delete on the bucket is not enabled. AWS only allows changing it through the CLI, SDK or API.



###### Next improvements: 



* Add GuardDuty (needs a paid plan), plus AWS Config and Security Hub for continuous checks
* Move human access to IAM Identity Center and keep IAM users for specific cases only
* Turn on CloudTrail data events for this bucket and S3 server access logging
* Add a bucket policy that denies requests not using HTTPS (aws:SecureTransport)

