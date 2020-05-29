# Boto3

The AWS SDK for Python

## List buckets in account

In VSCode console:

```
ipython
```
Then, 

```
In [1]: import boto3

# get profile list
In [2]: boto3.session.Session().available_profiles
Out[2]: ['training', 'default', 'master']

# access the ideal account
In [3]: account = boto3.session.Session(profile_name='training')
In [4]: s3 = account.resource('s3')
In [5]: for bucket in s3.buckets.all(): 
            print(bucket.name)
```

## List Stacks in Account

```
ipython
```
```
In [1]: import boto3

# get available accounts
In [2]: boto3.session.Session().available_profiles
Out[2]: ['training', 'default', 'master']

# access CloudFormation stacks
In [3]: account = boto3.session.Session(profile_name='training')
In [4]: cf = account.resource('cloudformation')
In [5]: for stack in cf.stacks.all():
   ...:     print(stack.name)
```