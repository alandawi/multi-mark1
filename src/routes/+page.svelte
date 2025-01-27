<script lang="ts">
    import { writable } from "svelte/store";

    let number = "";
    let option = "";
    let name = "";
    let mensajeFinal = "";

    let generatedText = writable("");

    const options = ["Pedido de Beca", "Renovación de Beca", "Darse de baja", "Entrevista con el Director", "Cobros", "Consulta sobre finales"];

    const optionTexts: Record<string, string> = {
        "Pedido de Beca": "Solicito formalmente una beca del tipo [DEFINIR_TIPO] para el próximo periodo académico. Entiendo que el periodo de solicitud es en febrero y quedo a disposición para cualquier información o documentación adicional que sea requerida.",
        
        "Renovación de Beca": "Por medio de la presente, solicito la renovación de mi beca. Adjunto mi número de matrícula para facilitar el proceso y quedo atento a cualquier requerimiento adicional necesario para completar la renovación.",
        
        "Darse de baja": "Quisiera solicitar formalmente la baja de la carrera en la que me encuentro inscripto/a. Agradeceré recibir la confirmación de esta solicitud y cualquier detalle adicional que deba considerar para completar el proceso.",
        
        "Entrevista con el Director": "Deseo coordinar una entrevista con el Director para tratar temas de interés relacionados con mi situación académica. Quedo a disposición para agendar la reunión en el horario y día que considere conveniente.",
        
        "Cobros": "Quisiera realizar una consulta respecto a la facturación de mi cuenta como estudiante. Agradecería que puedan brindarme información detallada sobre mi situación financiera o los próximos pasos a seguir.",
        
        "Consulta sobre finales": "Me gustaría plantear un inconveniente relacionado con las notas finales de mis evaluaciones. Solicito información sobre lo que podría estar ocurriendo y cómo proceder para resolver esta situación."
    };


    const generateText = () => {
        mensajeFinal = option ? optionTexts[option] || "" : "";

        if (number && option && name) {
            generatedText.set(`
                ${name} - Matrícula: ${number}
                <br /><br /><hr /><br />
                Asunto: ${option}
                <br /><br /><hr /><br />
                Mensaje:
                ${mensajeFinal}
            `);
        } else {
            generatedText.set("Por favor, completa todos los campos.");
        }
    };

    const copyToClipboard = async () => {
        if ($generatedText) {
            try {
                // Escribe el texto con formato HTML en el portapapeles
                await navigator.clipboard.write([
                    new ClipboardItem({
                        "text/html": new Blob([$generatedText], { type: "text/html" })
                    })
                ]);
                alert("Texto copiado al portapapeles como HTML.");
            } catch (err) {
                console.error("Error al copiar el texto:", err);
            }
        }
    };
</script>

<div class="mx-auto max-w-screen-xl px-4 py-16 sm:px-6 lg:px-8">
    <div class="mx-auto max-w-lg">
        <h1 class="text-center text-2xl font-bold text-indigo-600 sm:text-3xl">Generador de consultas multimediales</h1>

        <p class="mx-auto mt-4 max-w-md text-center text-gray-500">
            Esta mini aplicación fue creada para solucionar la falta de consultas ordenadas o completas que llegan por email. A medida que las consultas lleguen de forma ordenada, se podrá identificar un patrón y automatizar un proceso para una respuesta mucho mas rápida.
        </p>

        <form action="#" class="mb-0 mt-6 space-y-4 rounded-lg p-4 shadow-lg sm:p-6 lg:p-8">
            <input
                id="name"
                type="text"
                bind:value={name}
                placeholder="Ingresa tu nombre completo"
                class="mt-1 block w-full p-2 border border-gray-300 rounded-lg shadow-sm focus:ring-blue-500 focus:border-blue-500"
                on:input={generateText}
            />

            <input
                id="number"
                type="number"
                bind:value={number}
                placeholder="Ingresa tu matrícula"
                class="mt-1 block w-full p-2 border border-gray-300 rounded-lg shadow-sm focus:ring-blue-500 focus:border-blue-500"
                on:input={generateText}
            />

            <select
                id="option"
                bind:value={option}
                class="mt-1 block w-full p-2 border border-gray-300 rounded-lg shadow-sm focus:ring-blue-500 focus:border-blue-500"
                on:change={generateText}
            >
                <option value="">Selecciona una opción</option>
                {#each options as opt}
                    <option value={opt}>{opt}</option>
                {/each}
            </select>

            <div class="mt-4">
                <label class="block text-sm font-medium text-gray-600">Texto Generado</label>
                <div
                    class="mt-1 block w-full p-2 bg-gray-100 border border-gray-300 rounded-lg shadow-sm cursor-pointer hover:bg-gray-200"
                    on:click={copyToClipboard}
                >
                    {@html $generatedText}
                </div>
            </div>

            <p class="text-xs text-gray-500">Haz clic en el texto para copiarlo al portapapeles.</p>
        </form>
    </div>
</div>
