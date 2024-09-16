import json
import boto3
import time

# Crie o recurso DynamoDB usando o AWS SDK
dynamodb = boto3.resource('dynamodb')

# Selecione a tabela do DynamoDB
table = dynamodb.Table('HelloWorldDatabase')

# Função handler para o Lambda
def lambda_handler(event, context):
    try:
        # Obtenha o tempo atual no formato GMT
        gmt_time = time.gmtime()
        
        # Formate a hora em um formato legível
        now = time.strftime('%a, %d %b %Y %H:%M:%S +0000', gmt_time)
        
        # Verifique se o evento contém os dados necessários
        if 'tarefa' not in event or 'data' not in event:
            raise ValueError("Os campos 'tarefa' e 'data' são obrigatórios.")
        
        # Extraia os valores do evento
        tarefa = event['tarefa']
        data = event['data']
        
        # Insira os dados na tabela do DynamoDB
        response = table.put_item(
            Item={
                'tarefa': tarefa,
                'data': data,
                'LatestGreetingTime': now
            }
        )
        
        # Retorne uma resposta de sucesso
        return {
            'statusCode': 200,
            'headers': {
                'Content-Type': 'application/json'
            },
            'body': json.dumps(tarefa)
        }
    
    except Exception as e:
        # Retorne uma mensagem de erro em caso de exceção
        return {
            'statusCode': 400,
            'headers': {
                'Content-Type': 'application/json'
            },
            'body': json.dumps(f"Erro: {str(e)}")
        }