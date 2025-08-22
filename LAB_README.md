Laboratorio 4 - Spring Boot CI/CD con Jenkins y Docker

Instrucciones rápidas:
1. Requisitos: Jenkins con Docker disponible en el agente, demonio de Docker y Maven instalado en el agente.
2. Verificar el repositorio.
3. Ejecutar la canalización de Jenkins (Jenkinsfile) que:
- compila el proyecto (paquete mvn clean)
- ejecuta pruebas unitarias
- crea una imagen de Docker
- ejecuta el puerto de mapeo del contenedor 8080
4. Validar abriendo http://<jenkins-agent-host>:8080/ después de que el contenedor se inicie.

Entregables:
- Captura de la canalización con las etapas exitosas.
- URL de servicio (si es accesible) o salida de 'docker ps' que muestra el contenedor en ejecución.
- Jenkinsfile utilizado.