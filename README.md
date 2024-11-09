import dash
from dash import dcc, html
import pandas as pd
import plotly.express as px
from datetime import datetime, timedelta

# Simulando dados de fluxo de caixa (entradas e saídas)
data = {
    'data': [datetime.today() - timedelta(days=i) for i in range(30)],
    'entradas': [1000 + i * 50 for i in range(30)],
    'saidas': [500 + i * 30 for i in range(30)],
}

df = pd.DataFrame(data)

# Calculando o fluxo de caixa diário e o saldo de caixa
df['fluxo_caixa'] = df['entradas'] - df['saidas']
df['saldo_caixa'] = df['fluxo_caixa'].cumsum()

# Criando o aplicativo Dash
app = dash.Dash(__name__)

# Layout do dashboard
app.layout = html.Div([
    html.H1('Dashboard de Fluxo de Caixa', style={'text-align': 'center'}),
    
    # Gráfico de fluxo de caixa
    dcc.Graph(
        id='grafico_fluxo_caixa',
        figure=px.line(df, x='data', y=['entradas', 'saidas', 'fluxo_caixa'],
                      labels={'value': 'Valor', 'variable': 'Tipo de Fluxo'},
                      title='Fluxo de Caixa (Entradas, Saídas e Fluxo Diário)')
    ),
    
    # Resumo financeiro
    html.Div([
        html.Div([
            html.H3('Saldo de Caixa Atual:'),
            html.P(f'R$ {df["saldo_caixa"].iloc[-1]:,.2f}', style={'font-size': '24px'}),
        ], className='card'),
        
        html.Div([
            html.H3('Entradas Totais:'),
            html.P(f'R$ {df["entradas"].sum():,.2f}', style={'font-size': '24px'}),
        ], className='card'),
        
        html.Div([
            html.H3('Saídas Totais:'),
            html.P(f'R$ {df["saidas"].sum():,.2f}', style={'font-size': '24px'}),
        ], className='card'),
    ], style={'display': 'flex', 'justify-content': 'space-between', 'margin-top': '30px'}),
    
    # Tabela de contas a pagar e a receber (exemplo simples)
    html.H2('Contas a Pagar e Receber', style={'margin-top': '50px'}),
    html.Div([
        html.Div([
            html.H3('Contas a Pagar', style={'text-align': 'center'}),
            html.Table([
                html.Thead(html.Tr([html.Th('Vencimento'), html.Th('Valor')])),
                html.Tbody([html.Tr([html.Td(datetime.today().date() + timedelta(days=i)), html.Td(f'R$ {500 + i * 20:.2f}')]) for i in range(5)])
            ])
        ], style={'width': '48%', 'float': 'left'}),
        
        html.Div([
            html.H3('Contas a Receber', style={'text-align': 'center'}),
            html.Table([
                html.Thead(html.Tr([html.Th('Recebimento'), html.Th('Valor')])),
                html.Tbody([html.Tr([html.Td(datetime.today().date() + timedelta(days=i+10)), html.Td(f'R$ {1000 + i * 50:.2f}')]) for i in range(5)])
            ])
        ], style={'width': '48%', 'float': 'right'})
    ]),
])

# Executando o servidor Dash
if __name__ == '__main__':
    app.run_server(debug=True)


<!---
Brighiid/Brighiid is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
