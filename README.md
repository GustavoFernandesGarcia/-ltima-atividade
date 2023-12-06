from flask
import Flask, render_template, request
import sqlite3

class CalculadoraIMC:
    def __init__(self):
        self.app = Flask(__name__)

        @self.app.route('/')
        def index():
            return render_template('index.html')

        @self.app.route('/calcular_imc', methods=['POST'])
        def calcular_imc():
            if request.method == 'POST':
                peso = float(request.form['peso'])
                altura = float(request.form['altura'])
                imc = peso / (altura ** 2)

                self.adicionar_resultado_imc(peso, altura, imc)

                return render_template('resultado.html', imc=imc)

        def adicionar_resultado_imc(self, peso, altura, imc):
            conn = sqlite3.connect('imc.db')
            cursor = conn.cursor()

            cursor.execute('''
                CREATE TABLE IF NOT EXISTS resultados_imc (
                    id INTEGER PRIMARY KEY AUTOINCREMENT,
                    peso REAL,
                    altura REAL,
                    imc REAL
                )
            ''')

            cursor.execute('INSERT INTO resultados_imc (peso, altura, imc) VALUES (?, ?, ?)', (peso, altura, imc))

            conn.commit()
            conn.close()

    def run(self):
        self.app.run(debug=True)

if __name__ == '__main__':
    calculadora_imc = CalculadoraIMC()
    calculadora_imc.run()
