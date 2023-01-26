
---
#     #                              # 
#     #  ####### ###### #   #        #    ####### ####### #  #
#  #  #  #  #  # #      ####  ------ #### #  #  # #  #  # ###
### ###  ####### #      #   #        #### ####### ####### #  #
---
---
title: "s3 python example"
author: "w0rk-b00k"
---

The script has very basic functionality. It is a good starting point for s3 in AWS. 

```
from datetime import datetime
import sys
import random
import boto3
import botocore
def list_bucket():
    '''
    Lists the current buckets in S3.
    '''
    # Create an S3 Client
    s3 = boto3.client('s3')
    # Call S3 to list current buckets
    response = s3.list_buckets()
    # Get a list of all bucket names from the response
    buckets = [bucket['Name'] for bucket in response['Buckets']]
    # Print out the bucket lsit
    print('Bucket List: %s' % buckets)
def create_bucket():
    '''
    Creates a new bucket.
    '''
    # Create an S3 client
    s3 = boto3.client('s3')
    # Use user input for flexibility of the bucket's name 
    user_input = input('Enter your first and last name: ')
    # append the user input with a randomized number
    ran = random.randint(0,999999)
    # Sanity check
    # print(user_input + '-' + str(ran))
    # create the bucket
    s3.create_bucket(Bucket=user_input + '-' + str(ran))
    list_bucket()
def puts_objects():
    '''
    Puts an object a previously created bucket
    '''
    # Create an S3 client
    s3 = boto3.client('s3')
    # Takes in user input to name the file
    # Default text file to avoid UnboundLocalError
    file_create = 'default.txt'
    # Try/except to make sure the file doesn't exist.
    try:
        filename = input("What would you like the file to be called?: ")
        file_create = open(filename, 'x')
    except FileExistsError:
        print('That file already exists, please enter another file: ')
    print(file_create)
    list_bucket()
    # Lets the user pick the bucket
    bucket_name = input('Please choose the bucket you want: ')
    # Try/except for wrong bucket input
    try:
        s3.upload_file(filename, bucket_name, filename)
    except boto3.exceptions.S3UploadFailedError:
        print('This bucket could not be found...')
def delete_objects():
    '''
    Delete an object within a bucket
    '''
    s3 = boto3.client('s3')
    list_bucket()
    # Choose bucket
    try:
        bucket_name = input('Please choose the bucket you want: ')
    except boto3.exceptions.S3UploadFailedError:
        print("This bucket could not be found...")
    filename = input("What file would you like to delete?: ")
    response = s3.delete_object(Bucket=bucket_name, Key=filename)
    print('file' + filename + ' ' + 'has been deleted!')
def delete_buckets():
    '''
    Deletes a bucket
    '''
    list_bucket()
    s3 = boto3.resource('s3')
    # Bucket input for flexibility
    bucket_name = input('Please enter the name of buck you want to delete: ')
    # Choose the bucket from the s3 client
    bucket = s3.Bucket(bucket_name)
    # Delete bucket
    bucket.delete()
    print('Bucket' + bucket_name + ' ' + 'has been deleted!')
def copy_objects():
    '''
    Copy object(s) from one bucket to another
    '''
    s3 = boto3.resource('s3')
    list_bucket()
    # Choose the bucket and object
    bucket_name = input('Please enter the bucket you want to transfer from: ')
    object_name = input('Please enter the object you wish to transfer from: ')
    copy_source = {
        'Bucket': bucket_name,
        'Key': object_name
    }
    # Bucket to transfer to
    bucket_transfer = input('Please enter the bucket you wish to transfer to: ')
    name_object = input('Please enter the desired name of the object: ')
    bucket = s3.Bucket(bucket_transfer)
    bucket.copy(copy_source, name_object)
    print(name_object + ' ' + 'transfered!')
def download_object():
    s3 = boto3.resource('s3')
    list_bucket()
    bucket_name = input('Please choose what bucket you want to download from: ')
    object_name = input('Please enter the name of the object you wish to download: ')
    try:
        s3.Bucket(bucket_name).download_file(object_name, 'downloaded-' + object_name)
    except botocore.exceptions.ClientError as e:
        if e.response['Error']['Code'] == '404':
            print('The object does not exist...')
        else:
            raise
    print('The file has been downloaded...')
while 1:
    # The option varible will be used to select an action
    option = input('MENU\n'
                   'A. Create Bucket\n'
                   'B. List Buckets\n'
                   'C. Place Objects\n'
                   'D. Delete Objects\n'
                   'F. Delete Bucket\n'
                   'G. Copy Objects\n'
                   'H. Download Object\n'
                   'E. Exit\n')
    print('*********************************************************************')
    if option in ('A', 'a'):
        print("Create a bucket!")
        create_bucket()
        continue
    if option in ('B', 'b'):
        list_bucket()
        continue
    if option in ('C', 'c'):
        puts_objects()
        continue
    if option in ('D', 'd'):
        delete_objects()
        continue
    if option in ('F', 'f'):
        delete_buckets()
        continue
    if option in ('G', 'g'):
        copy_objects()
        continue
    if option in ('H', 'h'):
        download_object()
        continue
    if option in ('E', 'e'):
        print('Exiting the program, Goodbye!')
        date_time = datetime.fromtimestamp(1887639468)
        print(date_time)
        sys.exit()
    else:
        print('Please enter a menu option!')
        continue
    
```
