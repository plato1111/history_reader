
from flask import Flask, render_template, jsonify
from browser_history import get_history
from datetime import datetime, timedelta
import anthropic
import json
import logging


app = Flask(__name__)

# Configurar el registro de logging
logging.basicConfig(level=logging.INFO)

@app.route('/titulos')
def obtener_titulos():
    # Obtener la fecha de hace 7 días
    hace_una_semana = datetime.now().replace(tzinfo=None) - timedelta(days=7)
    
    # Obtener el historial de navegación desde hace una semana hasta ahora
    historiales = get_history()
    historiales = historiales.histories
    
    # Filtrar el historial para obtener solo los títulos
    titulos = []
    for historial in historiales:
        if historial[0].replace(tzinfo=None) > hace_una_semana:
            titulos.append(historial[2])  # Acceder al índice 2 para obtener el título
    
    # Crear un diccionario con la lista de títulos
    datos_json = {"titulos": titulos}
    
    return jsonify(datos_json)

@app.route('/respuesta')
def obtener_respuesta():
    # Obtener los títulos desde la ruta /titulos
    response = app.test_client().get('/titulos')
    datos_json = json.loads(response.data)
    titulos = datos_json["titulos"]
    
    # Crear el mensaje con los títulos
    mensaje_titulos = "Aquí está la lista de títulos:\n\n"
    for titulo in titulos:
        mensaje_titulos += f"- {titulo}\n"
    
    client = anthropic.Anthropic(
        api_key="KEY",
    )
    message = client.messages.create(
        model="claude-3-haiku-20240307",
        max_tokens=1000,
        temperature=0.0,
        system="Dame los primeros 5 titulos de lo que te doy.",
        messages=[
            {"role": "user", "content": mensaje_titulos}
        ]
    )
    
    # Obtener el contenido de la respuesta como una cadena de texto
    # respuesta = message.content

    respuesta = message.content[0].text

     # Imprimir la respuesta en lugar de devolverla como JSON
    #print(respuesta)

     # Registrar la respuesta usando logging
    logging.info("Respuesta recibida: %s", respuesta)
    
    return render_template('respuesta.html', respuesta=respuesta.strip())



if __name__ == '__main__':
    print("La aplicación Flask se está ejecutando en http://localhost:5000")
    app.run(debug=True)

