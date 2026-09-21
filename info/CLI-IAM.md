# Create an IAM user, group, and policy with the AWS CLI

```bash
aws iam create-group --group-name "GROUP_NAME"

aws iam get-group --group-name "GROUP_NAME"

aws iam create-user --user-name "USERNAME"

aws iam get-user --user-name "USERNAME"

aws iam add-user-to-group \
    --user-name "USERNAME" \
    --group-name "GROUP_NAME"

aws iam create-login-profile \
    --user-name "USERNAME" \
    --password "PASSWORD" \
    --password-reset-required

aws iam create-access-key --user-name "USERNAME"

# Query for the ARN of an existing policy
aws iam list-policies \
    --query 'Policies[?PolicyName == `AmazonEC2FullAccess`].Arn' \
    --output text

# Create a policy from a JSON file; see AWS-password-change-policy.json
aws iam create-policy \
    --policy-name ChangePassword \
    --policy-document file://AWS-password-change-policy.json

aws iam attach-group-policy \
    --group-name "GROUP_NAME" \
    --policy-arn "POLICY_ARN"

aws iam list-attached-group-policies --group-name "GROUP_NAME"
```

- To change the default user in the CLI, run `aws configure`.
- To change specific values, such as your access keys, without changing the default region, run `aws configure set AWS_ACCESS_KEY_ID <KEY_ID>`.
- To run commands as a specific user, set that user's keys as environment variables for the current session:

```bash
export AWS_ACCESS_KEY_ID=<KEY_ID>
export AWS_SECRET_ACCESS_KEY=<SECRET_KEY>
```