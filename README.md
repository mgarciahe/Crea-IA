<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Órbita Alfa</title>

    <link rel="stylesheet" href="/css/estilos.css">
</head>
<body>

    <div class="card">

        <h1>Sistema de Subastas</h1>

        <form action="/subasta" method="POST">

            <input type="number" name="idLote" placeholder="ID del lote" required>

            <input type="text" name="comerciante" placeholder="Comerciante" required>

            <input type="number" name="oferta" placeholder="Oferta" required>

            <button type="submit">Realizar Oferta</button>

        </form>

        <br>

        <a href="/consultar">
            <button>Consultar Mercado</button>
        </a>

    </div>

</body>
</html>
