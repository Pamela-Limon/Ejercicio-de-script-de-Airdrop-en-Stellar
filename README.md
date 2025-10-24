# Ejercicio-de-script-de-Airdrop-en-Stellar
Proyecto de práctica para aprender a realizar transacciones masivas (airdrop) en la red de prueba de Stellar usando JavaScript y el SDK oficial.
📋 Descripción
Este script permite enviar 10 XLM a 5 cuentas diferentes de forma automatizada. El programa es capaz de:

Verificar si las cuentas destino existen
Crear cuentas nuevas si no existen
Enviar pagos a cuentas existentes
Manejar errores y mostrar un resumen detallado

🎯 Desafío
Objetivo: Enviar 10 XLM a 5 cuentas diferentes en la red de prueba (Testnet) de Stellar.
Requisitos:

Tener una cuenta con suficiente balance (mínimo 51 XLM: 50 para enviar + 1 de reserva)
Generar 5 cuentas destino válidas
Implementar lógica para crear cuentas nuevas o enviar a cuentas existentes

🛠️ Tecnologías Utilizadas

Node.js v22.15.1
@stellar/stellar-sdk - SDK oficial de Stellar para JavaScript
JavaScript ES6+ - Async/await, arrow functions, template literals

📁 Estructura de Archivos
proyecto-stellar/
│
├── generar-5-cuentas.js          # Genera 5 pares de llaves válidos
├── airdrop-crear-cuentas.js      # Script principal del airdrop
├── verificar-cuenta.js            # Verifica balance de una cuenta
├── package.json                   # Dependencias del proyecto
└── README.md                      # Este archivo
🚀 Instalación

Clonar el repositorio:

bashgit clone https://github.com/tu-usuario/stellar-airdrop.git
cd stellar-airdrop

Instalar dependencias:

bashnpm install @stellar/stellar-sdk
📝 Uso
Paso 1: Generar Cuentas Destino
bashnode generar-5-cuentas.js
```

Este script generará 5 pares de llaves (pública/secreta) válidas para usar como destinatarios.

**Salida esperada:**
```
🔑 GENERANDO 5 CUENTAS PARA AIRDROP
===================================

✅ 5 cuentas generadas correctamente:

--- Cuenta 1 ---
Pública:  GBXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
Secreta:  SBXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
Paso 2: Configurar el Script de Airdrop
Abre airdrop-crear-cuentas.js y configura:
javascript// Tu cuenta que enviará los fondos
const CLAVE_SECRETA_ORIGEN = 'TU_CLAVE_SECRETA_AQUI';
const CLAVE_PUBLICA_ORIGEN = 'TU_CLAVE_PUBLICA_AQUI';

// Cuentas destino generadas en el paso anterior
const destinatarios = [
  'GBXXXX...', // Cuenta 1
  'GCXXXX...', // Cuenta 2
  'GDXXXX...', // Cuenta 3
  'GEXXXX...', // Cuenta 4
  'GFXXXX...'  // Cuenta 5
];
Paso 3: Ejecutar el Airdrop
bashnode airdrop-crear-cuentas.js
```

**Salida esperada:**
```
🔍 Verificando balance...

💰 Balance actual: 9989.9999900 XLM

✅ Balance suficiente!

🎁 INICIANDO AIRDROP (Con creación de cuentas)
===================

💰 Cantidad por cuenta: 10 XLM
📊 Total de cuentas: 5
💸 Total a enviar: 50 XLM

📤 Procesando cuenta 1/5...
   Destino: GBXXXXXXXX...
   ℹ️  La cuenta no existe, se creará
   ✅ Cuenta creada exitosamente!
   📝 Hash: a1b2c3d4e5f6...

[... se repite para las 5 cuentas ...]

===================
📊 RESUMEN DEL AIRDROP
===================
✅ Exitosos: 5/5
❌ Fallidos: 0/5
💰 Total enviado: 50 XLM
🧠 Conceptos Aprendidos
1. Diferencia entre createAccount y payment
javascript// createAccount - Para cuentas que NO existen
StellarSdk.Operation.createAccount({
  destination: cuenta,
  startingBalance: '10'  // Debe ser mínimo 1 XLM
})

// payment - Para cuentas que YA existen
StellarSdk.Operation.payment({
  destination: cuenta,
  asset: StellarSdk.Asset.native(),
  amount: '10'
})
¿Por qué? En Stellar, todas las cuentas deben mantener un balance mínimo de 1 XLM (reserva base). La operación createAccount crea la cuenta y la financia en un solo paso.
2. Firmas Criptográficas
Cada transacción debe ser firmada con tu clave secreta:
javascriptconst parLlaves = StellarSdk.Keypair.fromSecret(CLAVE_SECRETA);
transaccion.sign(parLlaves);
Esto demuestra criptográficamente que tú autorizas la transacción sin revelar tu clave secreta.
3. Número de Secuencia
Cada cuenta tiene un número de secuencia que aumenta con cada transacción:
javascriptconst cuentaOrigen = await server.loadAccount(CLAVE_PUBLICA_ORIGEN);
Esto previene:

Transacciones duplicadas
Ataques de replay
Mantiene el orden de las operaciones

4. Manejo de Errores
El script verifica si las cuentas existen antes de decidir qué operación usar:
javascripttry {
  await server.loadAccount(cuenta);
  cuentaExiste = true;  // Usar payment
} catch (error) {
  if (error.response && error.response.status === 404) {
    cuentaExiste = false;  // Usar createAccount
  }
}
🔒 Seguridad
⚠️ IMPORTANTE:

NUNCA subas tu clave secreta a GitHub
NUNCA compartas tu clave secreta con nadie
Usa variables de entorno para producción:

javascript  const CLAVE_SECRETA = process.env.STELLAR_SECRET_KEY;

Agrega .env a tu .gitignore

🌐 Red de Prueba vs Red Principal
Este proyecto usa Testnet (red de prueba):
javascriptnetworkPassphrase: StellarSdk.Networks.TESTNET
Para usar la red principal (con dinero real), cambia a:
javascriptnetworkPassphrase: StellarSdk.Networks.PUBLIC
const server = new StellarSdk.Horizon.Server('https://horizon.stellar.org');
⚠️ Advertencia: En la red principal usas dinero real. Practica siempre en Testnet primero.
📊 Resultados del Proyecto

✅ 5 cuentas creadas exitosamente
✅ 50 XLM distribuidos (10 XLM por cuenta)
✅ 0 errores en el proceso
✅ Tiempo de ejecución: ~5 segundos

🔗 Recursos Adicionales

Documentación oficial de Stellar
Stellar SDK para JavaScript
Stellar Laboratory - Herramienta visual para probar
Stellar Expert - Explorador de blockchain
Friendbot - Obtener XLM de prueba

🐛 Problemas Comunes
Error: "invalid checksum"
Causa: Las claves públicas no son válidas o están incompletas.
Solución: Genera nuevas claves con generar-5-cuentas.js
Error: "op_no_destination"
Causa: Intentaste usar payment en una cuenta que no existe.
Solución: Usa createAccount para cuentas nuevas.
Error: "tx_insufficient_balance"
Causa: No tienes suficiente XLM en tu cuenta origen.
Solución: Financia tu cuenta con Friendbot.
👨‍💻 Autor
Tu Nombre - @Pamela Limón 
actividad y código inicial de codigofutura, un programa de Tiburonas Builders

⭐ Si este proyecto te ayudó a aprender sobre Stellar, ¡no olvides darle una estrella!

🎓 Siguientes Pasos
Después de dominar este script, puedes intentar:

Airdrop con tokens personalizados (no solo XLM)
Airdrop condicional (solo a cuentas que cumplan ciertos criterios)
Airdrop masivo (más de 100 cuentas)
Dashboard web para visualizar los resultados
Integración con base de datos para rastrear destinatarios
