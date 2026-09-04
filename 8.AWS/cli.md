# AWS CLI Installation — macOS

## Install AWS CLI

```bash
curl -fsSL https://awscli.amazonaws.com/v2/install.sh | bash
```

## Verify Installation

```bash
aws --version
```

```bash
which aws
```

## If `aws` Is Not Found

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
```

```bash
source ~/.zshrc
```

```bash
aws --version
```

## Configure AWS CLI

```bash
aws configure
```

## Set AWS Region

```bash
aws configure set region ap-southeast-1
```

## Verify Region

```bash
aws configure get region
```

## Test AWS Credentials

```bash
aws sts get-caller-identity
```