<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Registro de Residentes - Yama</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
            background-color: #f0f8ff;
            color: #333;
            text-align: center;
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        h1 {
            color: #004080;
        }
        h2 {
            color: #00509e;
        }
        .container {
            width: 80%;
            max-width: 600px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        form, #detalles, #login {
            background: #d6e4ff;
            padding: 20px;
            border-radius: 10px;
            width: 100%;
            margin-top: 20px;
        }
        input, button, textarea {
            width: calc(100% - 20px);
            margin: 5px;
            padding: 10px;
            border: 1px solid #00509e;
            border-radius: 5px;
        }
        button {
            background-color: #00509e;
            color: white;
            cursor: pointer;
        }
        button:hover {
            background-color: #003366;
        }
        ul {
            list-style-type: none;
            padding: 0;
            width: 100%;
        }
        li a {
            text-decoration: none;
            color: #00509e;
        }
        li a:hover {
            text-decoration: underline;
        }
        #detalles, #registro {
            display: none;
        }
        .back-button {
            color: #004080;
            text-decoration: none;
            font-size: 20px;
            margin-top: 10px;
        }
        .back-button:hover {
            text-decoration: underline;
        }
        #notasAdicionales {
            margin-top: 20px;
        }
        #usuarioActual {
            font-size: 14px;
            color: #666;
            margin-top: 10px;
        }
        .eliminar-btn {
            background-color: #e63946;
            color: white;
            border: none;
            cursor: pointer;
            padding: 5px 10px;
            margin-top: 10px;
        }
        .eliminar-btn:hover {
            background-color: #d72b31;
        }
        .confirmar-eliminacion {
            display: none;
            margin-top: 10px;
        }
    </style>
</head>
<body>
    <h1>Registro De Yama</h1>
    <div class="container">
        <div id="login">
            <h2>Iniciar Sesión</h2>
            <form id="loginForm" autocomplete="off">
                <input type="text" id="usuario" placeholder="Usuario" required>
                <input type="password" id="password" placeholder="Contraseña" required>
                <button type="submit">Ingresar</button>
            </form>
        </div>
        <div id="registro">
            <h2>Añadir Residente</h2>
            <form id="registroForm" autocomplete="off">
                <input type="text" id="nombre" placeholder="Nombre" required>
                <input type="text" id="apellido" placeholder="Apellido" required>
                <input type="number" id="edad" placeholder="Edad" required>
                <input type="text" id="nacionalidad" placeholder="Nacionalidad" required>
                <button type="submit">Añadir Residente</button>
            </form>
            <h2>Residentes Registrados:</h2>
            <ul id="residentesList"></ul>
        </div>
        <div id="detalles">
            <a href="#" class="back-button" onclick="mostrarRegistro()">← Volver a la página anterior</a>
            <h1>Detalles del Residente</h1>
            <div id="infoResidente"></div>
            <h3>Comportamiento:</h3>
            <textarea id="comportamiento" rows="4" placeholder="Escribe sobre el comportamiento de este residente aquí..."></textarea>
            <button id="guardarComportamiento">Guardar Comportamiento</button>
            
            <!-- Añadir más detalles sobre el residente -->
            <div id="notasAdicionales">
                <h3>Notas Adicionales:</h3>
                <textarea id="notas" rows="4" placeholder="Añade más información sobre este residente aquí..."></textarea>
                <button id="guardarNotas">Guardar Notas</button>
            </div>

            <!-- Botón para eliminar residente -->
            <button id="eliminarResidente" class="eliminar-btn" onclick="confirmarEliminacion()">Eliminar Residente</button>
            <div id="confirmarEliminacionDiv" class="confirmar-eliminacion">
                <button onclick="eliminarResidente()">Sí, eliminar</button>
                <button onclick="cancelarEliminacion()">Cancelar</button>
            </div>
        </div>
        <!-- Aquí se mostrará el nombre del usuario actual -->
        <div id="usuarioActual"></div>
    </div>
    <script>
        const credenciales = {
            "Aarón García": "Deprec4ted!",
            "Mia Rojo": "Rita2025"
        };

        let residentes = JSON.parse(localStorage.getItem('residentes')) || [];
        let usuarioActual = null;
        let residenteActivo = null;

        // Mostrar el nombre del usuario actual
        function mostrarUsuario() {
            if (usuarioActual) {
                document.getElementById('usuarioActual').textContent = `Usuario Actual: ${usuarioActual}`;
            }
        }

        // Mostrar los residentes al cargar la página
        function cargarResidentes() {
            const listaResidentes = document.getElementById('residentesList');
            listaResidentes.innerHTML = '';
            residentes.forEach(residente => {
                agregarResidenteALista(residente);
            });
        }

        document.getElementById('loginForm').addEventListener('submit', function(event) {
            event.preventDefault();
            const usuario = document.getElementById('usuario').value;
            const password = document.getElementById('password').value;
            if (credenciales[usuario] && credenciales[usuario] === password) {
                usuarioActual = usuario;
                document.getElementById('login').style.display = 'none';
                document.getElementById('registro').style.display = 'block';
                document.getElementById('loginForm').reset();
                cargarResidentes(); // Cargar residentes guardados
                mostrarUsuario(); // Mostrar el usuario actual en la interfaz
            } else {
                alert('Usuario o contraseña incorrectos');
            }
        });

        document.getElementById('registroForm').addEventListener('submit', function(event) {
            event.preventDefault();
            const nombre = document.getElementById('nombre').value;
            const apellido = document.getElementById('apellido').value;
            const edad = document.getElementById('edad').value;
            const nacionalidad = document.getElementById('nacionalidad').value;
            const residente = { nombre, apellido, edad, nacionalidad };
            residentes.push(residente);
            localStorage.setItem('residentes', JSON.stringify(residentes)); // Guardar en localStorage
            agregarResidenteALista(residente);
            this.reset();
        });

        function agregarResidenteALista(residente) {
            const li = document.createElement('li');
            li.innerHTML = `<a href="#" onclick="mostrarDetalles('${encodeURIComponent(residente.nombre)}', '${encodeURIComponent(residente.apellido)}', '${residente.edad}', '${encodeURIComponent(residente.nacionalidad)}')">${residente.nombre} ${residente.apellido}</a>`;
            document.getElementById('residentesList').appendChild(li);
        }

        function mostrarDetalles(nombre, apellido, edad, nacionalidad) {
            residenteActivo = { nombre: decodeURIComponent(nombre), apellido: decodeURIComponent(apellido), edad, nacionalidad };
            document.getElementById('registro').style.display = 'none';
            document.getElementById('detalles').style.display = 'block';
            document.getElementById('infoResidente').innerHTML = `Nombre: ${residenteActivo.nombre}<br>Apellido: ${residenteActivo.apellido}<br>Edad: ${residenteActivo.edad}<br>Nacionalidad: ${residenteActivo.nacionalidad}`;
        }

        function mostrarRegistro() {
            document.getElementById('detalles').style.display = 'none';
            document.getElementById('registro').style.display = 'block';
        }

        document.getElementById('guardarComportamiento').addEventListener('click', function() {
            const comportamiento = document.getElementById('comportamiento').value;
            alert('Comportamiento guardado: ' + comportamiento);
        });

        document.getElementById('guardarNotas').addEventListener('click', function() {
            const notas = document.getElementById('notas').value;
            alert('Notas guardadas: ' + notas);
        });

        // Confirmar eliminación del residente
        function confirmarEliminacion() {
            document.getElementById('confirmarEliminacionDiv').style.display = 'block';
        }

        // Cancelar la eliminación
        function cancelarEliminacion() {
            document.getElementById('confirmarEliminacionDiv').style.display = 'none';
        }

        // Eliminar residente de la lista
        function eliminarResidente() {
            residentes = residentes.filter(residente => residente.nombre !== residenteActivo.nombre || residente.apellido !== residenteActivo.apellido);
            localStorage.setItem('residentes', JSON.stringify(residentes)); // Guardar cambios en localStorage
            cargarResidentes(); // Recargar la lista de residentes
            document.getElementById('detalles').style.display = 'none';
            document.getElementById('registro').style.display = 'block'; // Volver al listado de residentes
        }

        // Inicializar la página con los residentes
        cargarResidentes();
    </script>
</body>
</html>
