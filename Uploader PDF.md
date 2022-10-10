# UPLOADER PDF

Nos permite subir PDFs al directorio seleccionado.

## VISTA

    <div>
        <!-- INPUT -->
        <form method="post" enctype="multipart/form-data" id="formUploadPDF" style="display: inline;">
            <label for="archivoPDF" class="btn btn-danger" style="border-color: #000;">
                <span class="fa fa-file-pdf-o" style="margin-right: 10px;"></span>
                AÑADIR PDF
            </label>
            <input id="archivoPDF" name="archivoPDF" type="file" style="display: none">
        </form>

        <!-- BOTON PARA LIMPIAR PDF -->
        <button id="eliminar_pdf" class="btn btn-danger" style="border-color: #000;" title="LIMPIAR PDF AÑADIDO">
            <i class="fa fa-trash" style="color: #FFF;"></i>
        </button>

        <!-- BOTON PARA AÑADIR PDF -->
        <div class="preview-pdf" style="margin-top: 10px; min-height: 55px;"></div>
    </div>

## SCRIPT

### EVENTOS

    //BOTON LIMPIAR PDF (PAPELERA)
    $(document).on('click', '#eliminar_pdf', function() {
        limpiar_pdf()
    });


    // COMPROBAR EXTENSION AL SUBIR PDF
    $("#formUploadPDF input[type=file]").change(function() {
        /* EVENTO AL SELECCIONAR EL ARCHIVO COMPRUEBA LA EXTENSION */
        let file = this.files[0];

        if ($('.content-pdf').length != 0) {
            alert('Ya tienes un pdf subido');
        } else {
            if (file.type != "application/pdf") {
                alert('Tu archivo es: ' + file.type + '. El formato permitido es: pdf.');
                $('#archivoPDF').val('');
            } else {
                subir_pdf();
            }
        }
    });

### FUNCIONES

    //----------------------------------------------------
    // LIMPIAR PDF ---------------------------------------
    //----------------------------------------------------

    function limpiar_pdf() {
        var sName = $(".content-pdf").attr('data-name');
        var sId = $('#comunicado-id').val();
        var sRuta = '';
        // console.log({sId});

        if (sId != '') {
            sRuta = aComunicadosById[sId]['archivo'];
        }


        $.ajax({
            type: 'POST',
            data: {
                'file': sName,
                'ruta': sRuta
            },
            url: '<?= current_pagina() ?>/limpiar_pdf_',
            success: function(resp) {
                $("#formUploadPDF input[type=file]").val('');
                $(".content-pdf").remove();
            }
        });
    }


    //----------------------------------------------------
    // SUBIR PDF -----------------------------------------
    //----------------------------------------------------
    function subir_pdf() {

        var ruta = $('input[name^="archivoPDF"]').val();
        var data = new FormData();

        jQuery.each($('input[name^="archivoPDF"]')[0].files, function(i, file) {
            data.append(i, file);
        });

        $.ajax({
            type: 'POST',
            data: data,
            url: '<?= current_pagina() ?>/subir_pdf_',
            cache: false,
            contentType: false,
            processData: false,
            success: function(resultado) {
                resultado = JSON.parse(resultado);
                // console.log({resultado});

                if (resultado) {
                    let fPdf = '';
                    fPdf += '<div class="content-pdf" style="width: 100%; height:520px;" data-name="' + resultado + '" data-url="RUTA_URL' + resultado + '">';
                    fPdf += '<embed src="<?= base_url() ?>RUTA_URL' + resultado + '" type="application/pdf" width="100%" height="100%">';
                    fPdf += '</div>';
                    $(".preview-pdf").html(fPdf);
                }
            }
        });
    };

## INDEX

    function subir_pdf_()
    {
        $dir_subida     = "RUTA_URL";
        $nombre_pdf  = time() . "_" . $_FILES[0]['name'];
        $fichero_subido = $dir_subida . basename($nombre_pdf);
        $fichero_subido = str_replace(' ', '_', $fichero_subido);
        $nombre_pdf  = str_replace(' ', '_', $nombre_pdf);

        if (move_uploaded_file($_FILES[0]['tmp_name'], $fichero_subido)) {
            echo json_encode($nombre_pdf);
        } else {
            echo json_encode(false);
        }
    }

    // LIMPIAR PDF
    function limpiar_pdf_()
    {
        $fFile = trim($_POST['file']);
        $sRuta = trim($_POST['ruta']);

        // echo "RUTA    -> ".$sRuta."\n";
        // echo "FICHERO -> ".$fFile."\n";
        // echo "DIR     -> ".$sDir ."\n";

        if ($sRuta != '') {
            if ($fFileComunicado = fopen($sRuta, "w+")) {
                if (fwrite($fFile, "")) {
                    $bSuccess = true;
                } else {
                }
            };
        }

        $sDir  = "RUTA_URL" . $fFile;
        unlink($sDir);
    }