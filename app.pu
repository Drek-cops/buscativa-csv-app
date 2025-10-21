from flask import Flask, render_template, request, redirect, url_for, session
import csv
import os
from datetime import datetime

app = Flask(__name__)
app.secret_key = "chave_secreta_segura"

# Arquivo com usuários
MONITORES_CSV = "monitores.csv"

def carregar_monitores():
    monitores = {}
    with open(MONITORES_CSV, newline='', encoding='utf-8') as f:
        leitor = csv.DictReader(f, delimiter=';')
        for linha in leitor:
            monitores[linha['usuario']] = {
                'senha': linha['senha'],
                'nome': linha['nome_completo'],
                'turno': linha['turno']
            }
    return monitores

@app.route('/', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        usuario = request.form['usuario']
        senha = request.form['senha']
        monitores = carregar_monitores()
        if usuario in monitores and monitores[usuario]['senha'] == senha:
            session['usuario'] = usuario
            return redirect(url_for('registro'))
        else:
            return render_template('login.html', erro="Usuário ou senha incorretos.")
    return render_template('login.html')

@app.route('/registro', methods=['GET', 'POST'])
def registro():
    if 'usuario' not in session:
        return redirect(url_for('login'))

    usuario = session['usuario']
    monitores = carregar_monitores()
    monitor = monitores[usuario]

    if request.method == 'POST':
        aluno = request.form['aluno']
        data = request.form['data']
        motivo = request.form['motivo']

        nome_arquivo = f"faltas_{datetime.now().strftime('%B_%Y').lower()}.csv"
        existe = os.path.exists(nome_arquivo)
        with open(nome_arquivo, 'a', newline='', encoding='utf-8') as f:
            escritor = csv.writer(f, delimiter=';')
            if not existe:
                escritor.writerow(['Monitor', 'Turno', 'Aluno', 'Data', 'Motivo'])
            escritor.writerow([monitor['nome'], monitor['turno'], aluno, data, motivo])

        return render_template('registro.html', monitor=monitor, sucesso=True)

    return render_template('registro.html', monitor=monitor)

@app.route('/logout')
def logout():
    session.pop('usuario', None)
    return redirect(url_for('login'))

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
