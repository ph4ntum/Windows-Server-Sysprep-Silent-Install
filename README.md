# Laboratorio 6: Automatización de Despliegue de Imágenes e Instalaciones Silenciosas

Este laboratorio práctico documenta la resolución de tareas de infraestructura corporativa, enfocado en el empaquetado de sistemas operativos para despliegue masivo y la distribución automatizada de software en segundo plano.

---

## 🛠️ Ticket 1: Generalización del Sistema con Sysprep (Imagen Maestra)

### 📋 Objetivo
Preparar e inicializar una instancia limpia de Windows Server 2022 para ser utilizada como "Imagen Maestra" (Molde base), eliminando los identificadores únicos de seguridad (SIDs) y preparando el sistema operativo para un despliegue masivo y agnóstico de hardware.

### ⚙️ Implementación Técnica
Se ejecutó la Herramienta de Preparación del Sistema (`Sysprep.exe`) configurando los siguientes parámetros mandatorios para asegurar una clonación limpia dentro de una infraestructura de red:

* **OOBE (Out-of-Box Experience):** Configura el sistema operativo para que, en su próximo arranque, muestre el asistente de bienvenida de fábrica, permitiendo asignar nuevos nombres de equipo y credenciales administrativas locales independientes.
* **Generalize (Generalizar):** Elimina la información específica de hardware, logs criptográficos, la base de datos de activación y el SID único, evitando colisiones de identidad en el Directorio Activo (Active Directory).
* **Shutdown (Apagar):** Apaga el servidor inmediatamente después del sellado, dejándolo en un estado estático óptimo para capturar el disco virtual.

# Acceso al directorio nativo y ejecución de la herramienta desde PowerShell
```powershell
cd C:\Windows\System32\Sysprep
.\sysprep.exe
```

A continuación se muestra la interfaz de Sysprep parametrizada correctamente antes de iniciar el proceso de generalización:

<img width="1920" height="972" alt="ticket1_sysprep_config" src="https://github.com/user-attachments/assets/ad6f1b82-9d2a-433e-99dd-5e6a81703672" />

---

## 📦 Ticket 2: Despliegue Desatendido e Instalación Silenciosa (.msi)

### 📋 Objetivo
Realizar la instalación de la herramienta corporativa de compresión **7-Zip** de forma masiva en segundo plano, garantizando que el proceso no interrumpa las tareas de los usuarios ni requiera interacción humana en la pantalla (instalación desatendida).

### ⚙️ Implementación Técnica
Para lograr un despliegue invisible al usuario, se utilizó un paquete instalador en formato **Windows Installer (`.msi`)**, el cual permite la inyección de argumentos lógicos por línea de comandos mediante el motor de Windows `msiexec`.

#### 1. Verificación del Paquete de Instalación
Se descargó el instalador oficial de 64 bits en el directorio de descargas local del servidor:

<img width="1155" height="592" alt="ticket2_download_7zip" src="https://github.com/user-attachments/assets/dfd17865-35e3-41fb-bdff-2e38cfd6dd85" />


#### 2. Ejecución del Comando Silencioso
Desde una consola de **PowerShell con privilegios de Administrador**, se lanzó la instalación en segundo plano utilizando modificadores avanzados:

```powershell
msiexec /i "C:\Users\Administrator\Downloads\7z2601-x64.msi" /qn /norestart
```

/i: Indica al motor la acción de instalar el paquete especificado.

/qn: Modificador Quiet con No UI (Sin interfaz de usuario). Suprime por completo las ventanas gráficas, barras de progreso y alertas emergentes, forzando la ejecución en segundo plano.

/norestart: Bloquea cualquier solicitud o intento de reinicio automático del sistema operativo tras finalizar la instalación, previniendo la pérdida de datos del usuario.

#### 3. Auditoría y Verificación del Éxito del Ticket
Dado que la instalación opera sin interfaz visual, se auditó el directorio de instalación mediante la CLI para confirmar el correcto despliegue de los binarios y librerías del sistema:

```powershell
Get-ChildItem "C:\Program Files\7-Zip"
```

<img width="932" height="619" alt="ticket2_install_silence" src="https://github.com/user-attachments/assets/9ac09373-7107-408c-94d8-2e822832501f" />

Como se observa en la consola, la estructura completa de archivos (7zFM.exe, 7z.dll, etc.) fue generada exitosamente sin desplegar ninguna ventana interactiva.

---

## 🎯 Habilidades Consolidadas

1. **Gestión del Ciclo de Vida de OS:** Uso de herramientas de ingeniería de software nativas (`Sysprep`) para el correcto empaquetado, sellado e industrialización de imágenes corporativas, evitando la duplicidad de identidades (SIDs) en redes empresariales.
2. **Automatización de Despliegues (Zero-Touch):** Manipulación avanzada de instaladores corporativos (`.msi`) y control de argumentos lógicos del motor `msiexec` para ejecutar tareas de mantenimiento e instalación desatendida a gran escala sin impactar en la productividad del usuario final.
3. **Auditoría por Línea de Comandos:** Verificación estructural del sistema de archivos mediante PowerShell (`Get-ChildItem`) para validar cambios de estado de forma técnica y profesional sin depender de interfaces gráficas.
