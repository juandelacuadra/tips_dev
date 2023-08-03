# AUTOCOMPLETAR

Autocompleta un buscador. PE: Usuarios, localidades...

## VISTA

    <div class="input-group">
        <input type="text" id="usuario_boletos" class="form-control" placeholder="SELECCION DE USUARIO">
        <span class="input-group-btn">
            <button id="btn_consultar_usuario" class="btn btn-success" type="button">
                <span class="fa fa-search"></span>
            </button>
        </span>
    </div>

## SCRIPT

    cargar_autocomplete();

    function cargar_autocomplete() {

        // PETICION AJAX
        $.ajax({
            url: '<?= current_pagina() ?>/cargar_autocomplete_',
            type: 'POST',
            data: {},
            beforeSend: function() {
                // $(".loader_ajax").show();
            },
            success: function(data) {

                data = JSON.parse(data);
                agentes = data['agentes'];

                // AUTOCOMPLETADO DE CAMPO DE BUSQUEDA DE AGENTE
                $('#usuario_boletos').autocomplete({
                    minLength: 2,
                    source: agentes
                });
            },
        });
    }

## INDEX

    function cargar_autocomplete_()
    {
        $modelo = new ClaseDatos();

        //CONSULTO AGENTES
        $sql =
            "SELECT DISTINCT
                USUARIO
            FROM
                BPANDOF_ALISSEN.TOMBOLA_VENTAS_KPI";

        $resultado_agentes = $modelo->query($sql, 'bd_force', 'assoc');
        $agentes_index = array_por_id($resultado_agentes, 'USUARIO');

        // ... Y LOS AÑADO AL ARRAY
        $agentes = [];
        foreach ($agentes_index as $key => $value) {
            foreach ($value as $key2 => $value2) {
                array_push($agentes, $value2);
            }
        }

        // FORMO EL ARRAY PARA DEVOLVER LOS DATOS
        $datos = array(
            'agentes' => $agentes
        );
        echo json_encode($datos);
    }