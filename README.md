1# Passo 1 - Importando as bibliotecas necessárias
2import telegram
3import time
4from datetime import datetime
5from bet365api import Bet365
6
7# Passo 2 - Definir as credenciais do Bot do Telegram
8bot = telegram.Bot(token='6102282337:AAEXP0mBvkgIhoCSDSPvmCRLFuBHftj0QoA')
9
10# Passo 3 - Definir as credenciais de acesso à API do Bet365
11USERNAME = 'alvrimperdido'
12PASSWORD = 'Galo123'
13SPORT_ID = 1  # ID do esporte = futebol
14
15# Passo 4 - Função para verificar as oportunidades de aposta
16def verificar_oportunidades_aposta(client, league_ids):
17    odds = []
18    for league_id in league_ids:
19        try:
20            res = client.get_top_events_live_v3(sport_id=SPORT_ID, league_id=league_id, event_type_ids=[8, 12])
21            data = res.json()
22            for item in data['results']:
23                try:
24                    for offer in item['offers']:
25                        try:
26                            if offer['type'] == 'Over/Under':
27                                if float(offer['ml'][0][0]) >= 1.61:
28                                    prob = float(offer['ml'][1][0])
29                                    if prob >= 0.8:
30                                        odds.append({
31                                            'league_name': item['league']['name'],
32                                            'event_name': item['ss'],
33                                            'bet_type': offer['md'][0],
34                                            'bet_value': float(offer['ml'][0][0]),
35                                            'probability': prob,
36                                        })
37                                break
38                        except:
39                            pass
40                except:
41                    pass
42        except:
43            pass
44    return odds
45
46# Passo 5 - Função para enviar mensagem no Telegram
47def enviar_mensagem(odds):
48    for odd in odds:
49        message = f'⚽ Jogo: {odd["event_name"]}\n📍 Liga: {odd["league_name"]}\n💰 Tipo de aposta: {odd["bet_type"]}\n📈 Odd: {odd["bet_value"]}\n💯 Probabilidade: {odd["probability"]:.0%}\n\n'
50        bot.send_message(chat_id='@1113862591', text=message)
51
52# Passo 6 - Loop infinito para verificar as oportunidades de aposta
53if name == '__main__':
54    while True:
55        try:
56            client = Bet365(USERNAME, PASSWORD)
57            league_ids = ['885', '886']  # IDs da e-soccer 8 minutos e 12 minutos
58            odds = verificar_oportunidades_aposta(client, league_ids)
59            if odds:
60                enviar_mensagem(odds)
61        except Exception as e:
62            print(f'[ERRO]: {e}')
63        time.sleep(60)  # Verificar a cada 60 segundos
64
