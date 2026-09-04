# AWS CLI Installation — macOS

## Install AWS CLI

curl -fsSL https://awscli.amazonaws.com/v2/install.sh | bash

## Verify Installation

aws --version

which aws

## If `aws` Is Not Found

echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc

source ~/.zshrc

aws --version

## Configure AWS CLI

aws configure

## Set AWS Region

aws configure set region ap-southeast-1

## Verify Region

aws configure get region

## Test AWS Credentials

aws sts get-caller-identity