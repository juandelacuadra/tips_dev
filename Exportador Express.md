## EXPORTADOR EXPRESS

Este modelo de exportador admite filtros y genera automáticamente un archivo excel con el resultado de la consulta.

### VISTA

    <button type="button" id="exportar_informe" class="btn btn-warning">EXPORTAR EL INFORME</button>

El ***id*** es el que lanza el evento. La clase del botón se puede cambiar depende del color que queramos.



### SCRIPT

Pasamos la información a servidor por URL, hay dos opciones:

#### Sin parámetros:

    $(document).off('click', '#exportar_informe').on('click', '#exportar_informe', function() {
        window.location.href = "<?= current_pagina() ?>/exportInforme_";
    });

**¡Ojo!** Esta opción nos trae todos los valores de la tabla.

#### Con parámetros:

     $(document).off('click', '#exportar_informe').on('click', '#exportar_informe', function() {3

        // INPUTS DE FECHA
        var fecha_desde = $('#exportar_desde').val();
        var fecha_hasta = $('#exportar_hasta').val();

        // VALIDACIÓN
        if (fecha_desde == '' || fecha_hasta == '') {
            alert('Hay que seleccionar una fecha');
        } else {
            window.location.href = "<?= current_pagina() ?>/exportInforme_/" + fecha_desde + "/" + fecha_hasta;
        }

    });

**¡Ojo!** Se concatenan los parámetros por URL

### INDEX

**Recuerda que si traemos algún parámetro hay que incluirlo en la query.**

    function exportInforme_()
    {

        $modelo = new ClaseDatos();
        $sql = "
            SELECT 
                * 
            FROM 
                bd_viki.NOMBRE_DE_LA_TABLA
            WHERE 
                DATE(cts) BETWEEN '" . $fecha_desde . "' AND '" . $fecha_hasta . "'";
            ";
        $tabla = $modelo->query($sql);

        $hoy = date('Y-m-d');

        header("Cache-Control: no-cache, must-revalidate");
        header("Expires: Mon, 26 Jul 1997 05:00:00 GMT");

        // === AQUI SE NOMBRA EL ARCHIVO DESCARGABLE === //
        $_archivo = "NOMBRE_DEL_INFORME_" . $hoy . '_viki.xls';
                
        header("Last-Modified: " . gmdate("D,d M YH:i:s") . " GMT");
        header("Cache-Control: no-cache, must-revalidate");
        header("Pragma: no-cache");
        header("Content-type: application/x-msexcel");
        header("Content-Disposition: attachment; filename=" . $_archivo . "");
        header("Content-Description: PHP Generated Data");

        echo "<table>";
        echo "<tr >";
        foreach ($tabla[0] as $key => $value) {
            // foreach ($tabla as $key => $value) {
            if (!is_numeric($key)) {
                echo "<td style='background:#589CCB;color:white;padding:10px'>" . utf8_decode($key) . "</td>";
                $cabeceras[] = $key;
            }
        }
        echo "</tr>";

        for ($i = 0; $i < count($tabla); $i++) {
            echo "<tr style='border-collapse:collapse;'>";
            for ($j = 0; $j < count($cabeceras); $j++) {
                echo "<td style=''>" . utf8_decode($tabla[$i][$cabeceras[$j]]) . "</td>";
            }
            echo "</tr>";
        }
        echo "</table>";
    }

