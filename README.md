const express = require('express');
const path = require('path');

const app = express();

const conexion = require('./db');

app.use(express.urlencoded({ extended: true }));

app.use(express.static(path.join(__dirname, 'public')));

app.get('/', (req, res) => {
    res.sendFile(path.join(__dirname, 'public/index.html'));
});

app.post('/subasta', (req, res) => {

    const { idLote, comerciante, oferta } = req.body;

    const consultaLote = new Promise((resolve, reject) => {

        conexion.query(
            'SELECT * FROM lotes WHERE id = ?',
            [idLote],
            (error, resultado) => {

                if(error) reject(error);
                else resolve(resultado);

            }
        );

    });

    const consultaComerciante = new Promise((resolve, reject) => {

        conexion.query(
            'SELECT * FROM comerciantes WHERE nombre = ?',
            [comerciante],
            (error, resultado) => {

                if(error) reject(error);
                else resolve(resultado);

            }
        );

    });

    Promise.all([consultaLote, consultaComerciante])

    .then(([loteResultado, comercianteResultado]) => {

        if(loteResultado.length === 0){
            return res.send('<h1>Error: Lote no existe</h1>');
        }

        if(comercianteResultado.length === 0){
            return res.send('<h1>Error: Comerciante no existe</h1>');
        }

        const lote = loteResultado[0];
        const usuario = comercianteResultado[0];

        if(parseInt(oferta) > usuario.saldo){
            return res.send('<h1>Error: Saldo insuficiente</h1>');
        }

        if(parseInt(oferta) <= lote.puja_actual){
            return res.send('<h1>Error: La oferta debe ser mayor a la actual</h1>');
        }

        const nuevoSaldo = usuario.saldo - parseInt(oferta);

        const actualizarSaldo = new Promise((resolve, reject) => {

            conexion.query(
                'UPDATE comerciantes SET saldo = ? WHERE nombre = ?',
                [nuevoSaldo, comerciante],
                (error, resultado) => {

                    if(error) reject(error);
                    else resolve(resultado);

                }
            );

        });

        const actualizarLote = new Promise((resolve, reject) => {

            conexion.query(
                'UPDATE lotes SET puja_actual = ?, lider = ? WHERE id = ?',
                [oferta, comerciante, idLote],
                (error, resultado) => {

                    if(error) reject(error);
                    else resolve(resultado);

                }
            );

        });

        Promise.all([actualizarSaldo, actualizarLote])

        .then(() => {

            res.send(`
                <h1>Subasta realizada con éxito</h1>
                <a href="/">Volver</a>
            `);

        })

        .catch((error) => {

            res.send('Error al actualizar');

        });

    })

    .catch((error) => {

        res.send('Error del servidor');

    });

});

app.get('/consultar', (req, res) => {

    const obtenerLotes = new Promise((resolve, reject) => {

        conexion.query(
            'SELECT * FROM lotes',
            (error, resultado) => {

                if(error) reject(error);
                else resolve(resultado);

            }
        );

    });

    const obtenerComerciantes = new Promise((resolve, reject) => {

        conexion.query(
            'SELECT * FROM comerciantes',
            (error, resultado) => {

                if(error) reject(error);
                else resolve(resultado);

            }
        );

    });

    Promise.all([obtenerLotes, obtenerComerciantes])

    .then(([lotes, comerciantes]) => {

        let tablaLotes = '';

        lotes.forEach(lote => {

            tablaLotes += `
                <tr>
                    <td>${lote.id}</td>
                    <td>${lote.descripcion}</td>
                    <td>${lote.puja_actual}</td>
                    <td>${lote.lider}</td>
                </tr>
            `;

        });

        let tablaComerciantes = '';

        comerciantes.forEach(usuario => {

            tablaComerciantes += `
                <tr>
                    <td>${usuario.id}</td>
                    <td>${usuario.nombre}</td>
                    <td>${usuario.saldo}</td>
                </tr>
            `;

        });

        res.send(`
            <h1>Estado del Mercado</h1>

            <h2>Lotes</h2>

            <table border="1">
                <tr>
                    <th>ID</th>
                    <th>Descripción</th>
                    <th>Puja</th>
                    <th>Líder</th>
                </tr>

                ${tablaLotes}
            </table>

            <h2>Comerciantes</h2>

            <table border="1">
                <tr>
                    <th>ID</th>
                    <th>Nombre</th>
                    <th>Saldo</th>
                </tr>

                ${tablaComerciantes}
            </table>

            <br>

            <a href="/">Volver</a>
        `);

    })

    .catch((error) => {

        res.send('Error al consultar');

    });

});

app.listen(3000, () => {
    console.log('Servidor corriendo en puerto 3000');
});
