const mysql = require('mysql2');

const conexion = mysql.createConnection({
    host: 'localhost',
    user: 'root',
    password: '',
    database: 'orbita_alfa'
});

conexion.connect((error) => {
    if(error){
        console.log('Error de conexión');
    }else{
        console.log('Conectado a MySQL');
    }
});

module.exports = conexion;
