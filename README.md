# aws-profile

Wrapper script to generate &amp; pass AWS AssumeRole keys to other scripts

## Example usage

There are three accounts:

- PRIMARY
- DEVELOPMENT
- PRODUCTION

IAM users are created in PRIMARY account with delegated access to DEVELOPMENT/PRODUCTION


### Credentials of IAM user defined in PRIMARY account

```
// ~/.aws/credentials
[first.lastname]
aws_access_key_id=abcdef
aws_secret_access_key=abc123
```

### Profile configs for Project X

```
// ~/.aws/config
# User in PRIMARY account assuming role in PRODUCTION
[profile projectX-prd]
role_arn = arn:aws:iam::$PRODUCTION:role/projectX
source_profile = first.lastname
 
# user in PRIMARY account assuming role in DEVELOPMENT
[profile projectX-dev]
role_arn = arn:aws:iam::$DEVELOPMENT:role/projectX
source_profile = first.lastname
```

### For DEVELOPMENT environment
```
// main.tf 
#  configure provider to only allow users or users with assumed roles from the DEVELOPMENT account
provider "aws" {
    [...]
    allowed_account_ids = [ "$DEVELOPMENT" ]
}
```

### Running projectX via aws-profile

```
pushd ./development
 
# this works as expected
AWS_DEFAULT_PROFILE=projectX-dev aws-profile projectX plan
 
# this should _not_ work, but does, and creates resources in the PRODUCTION account
AWS_DEFAULT_PROFILE=projectX-prd aws-profile projectX plan
```
