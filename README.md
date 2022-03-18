**Deploy an [Architect](https://arc.codes) project from GitHub Actions with keys gathered from a specific AWS IAM Role federated by an IAM OIDCProvider.**

CloudFormation to creat the GitHub OIDCProvider and an IAM Role:

```yml
Parameters:
  FullRepoName:
    Type: String
    Default: tbeseda/arc-gha-aws-oidc
Resources:
  Role:
    Type: 'AWS::IAM::Role'
    Properties:
      RoleName: github
      ManagedPolicyArns:
        - 'arn:aws:iam::aws:policy/AdministratorAccess'
      AssumeRolePolicyDocument:
        Statement:
          - Effect: Allow
            Action: 'sts:AssumeRoleWithWebIdentity'
            Principal:
              Federated: !Ref GithubOidc
            Condition:
              StringLike:
                'token.actions.githubusercontent.com:sub': !Sub 'repo:${FullRepoName}:*'
  GithubOidc:
    Type: 'AWS::IAM::OIDCProvider'
    Properties:
      Url: 'https://token.actions.githubusercontent.com'
      ThumbprintList:
        - 6938fd4d98bab03faadb97b34396831e3780aea1
      ClientIdList:
        - 'sts.amazonaws.com'
Outputs:
  Role:
    Value: !GetAtt Role.Arn

```

See [`.github/workflows/deploy.yml`](./.github/workflows/deploy.yml) for usage.

Resources

* [aws-actions/configure-aws-credentials](https://github.com/aws-actions/configure-aws-credentials)
* Addresses ['remix-run/grunge-stack#20'](https://github.com/remix-run/grunge-stack/issues/20)
