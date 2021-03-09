# temprepo

import cv2
import boto3

ACCESS_KEY = ""
SECRET_KEY =  ""

s3 = boto3.client('s3', aws_access_key_id=ACCESS_KEY,
                  aws_secret_access_key=SECRET_KEY)


image = cv2.imread(r"C:\Users\shash\Downloads\IMG_20201005_230333.jpg")


image_string = cv2.imencode('.jpg', image)[1].tostring()

s3.put_object(Bucket="deep-ocr-files", Key = "test_image.jpg", Body=image_string)



############ Dynamodb
from boto3.dynamodb.conditions import Key

dynamodb = boto3.resource('dynamodb', region_name = "us-west-2", aws_access_key_id=ACCESS_KEY,aws_secret_access_key=SECRET_KEY)


table = dynamodb.create_table(
    TableName='image_data',
    KeySchema=[
        {
            'AttributeName': 'id',
            'KeyType': 'HASH'
        },
    ],
    AttributeDefinitions=[
        {
            'AttributeName': 'id',
            'AttributeType': 'N'
        },
    ],
    ProvisionedThroughput={
        'ReadCapacityUnits': 1,
        'WriteCapacityUnits': 1,
    }
)


print("Table status:", table.table_status)


#################### Put data in table
user = {
    'id': 4,
    'first_name': 'Ankit',
    'last_name': 'Namdeo',
    'image_count' : 10,
    'Result': 'Fake',
    'New_field': 'sds'}

dynamodb = boto3.resource('dynamodb', region_name = "us-west-2", aws_access_key_id=ACCESS_KEY,aws_secret_access_key=SECRET_KEY)

table = dynamodb.Table('image_data')

table.put_item(Item=user)


############## Get data

table = dynamodb.Table('image_data')

resp = table.get_item(
    Key={
        'id': 1,
    }
)

if 'Item' in resp:
    print(resp['Item'])

################### Query data

table = dynamodb.Table('image_data')

resp = table.query(
    =Key('Result').eq('live')
)

if 'Items' in resp:
    print(resp['Items'][0])

response = table.get_item(Key={'Result': 'live'})

############### pdate

table.update_item(
    Key={
        'id': 1,
    },
    UpdateExpression="set first_name = :g",
    ExpressionAttributeValues={
        ':g': "Jane"
    },
    ReturnValues="UPDATED_NEW"
)


########## Delete


response = table.delete_item(
    Key={
        'id': 1,
    },
)
response
