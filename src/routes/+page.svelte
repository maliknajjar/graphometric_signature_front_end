<!-- DrawCanvas.svelte -->
<script lang="ts">
    import { onMount } from "svelte";

    let x: any = 0;
    let y: any = 0;
    let time: any = 0;
    let real_speed: any = 0;
    let speed: any = 0;
    let pressure: any = 0;
    let real_pressure: any = 0;
    let tiltX: any = 0;
    let tiltY: any = 0;
    let twist: any = 0;

    let signatureData: any = [];

    onMount(() => {
        const canvas = document.querySelector('canvas') as HTMLCanvasElement;
        const ctx = canvas.getContext('2d');

        if (!ctx) {
            alert('Canvas Context is null')
            throw new Error("Context is null");
        }

        let prev_x: number = 0;
        let prev_y: number = 0;
        let prev_time: number = 0;

        ctx.lineWidth = 2;

        let isDrawing = false;

        canvas.addEventListener('pointerdown', function(event) {
            isDrawing = true;
            const x = event.clientX - canvas.offsetLeft;
            const y = event.clientY - canvas.offsetTop;
            ctx.beginPath();
            ctx.moveTo(x, y);
        });

        canvas.addEventListener('pointermove', function(event) {
            if (!isDrawing) return;
            x = event.clientX - canvas.offsetLeft;
            y = event.clientY - canvas.offsetTop;
            time = event.timeStamp;
            const distance = Math.sqrt(Math.pow(x - prev_x, 2) + Math.pow(y - prev_y, 2)); 
            const timeDiff = time - prev_time;
            real_speed = distance / timeDiff;
            speed = real_speed.toFixed(4);

            real_pressure = event.pressure;
            pressure = real_pressure.toFixed(4);
            tiltX = event.tiltX;
            tiltY = event.tiltY;
            twist = event.twist;

            signatureData.push([
                x,
                y,
                time,
                real_speed,
                real_pressure,
                tiltX,
                tiltY,
                twist,
            ]);

            ctx.lineTo(x, y);
            ctx.stroke();

            // set previous coordinates
            prev_x = x;
            prev_y = y;
            prev_time = time;
        });

        canvas.addEventListener('pointerup', function() {
            isDrawing = false;
        });

        canvas.addEventListener('pointerleave', function() {
            isDrawing = false;
        });
    });

    function clearCanvas() {
        const canvas = document.querySelector('canvas') as HTMLCanvasElement;
        var ctx = canvas.getContext('2d');
        if (!ctx) {
            alert('Canvas Context is null')
            throw new Error("Context is null");
        }

        // Clear the entire canvas
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        
        // clear saved signature data
        signatureData = [];
    }

    function arrayToCSV(data: any) {
        return data.map((row: any) => row.join(',')).join('\n');
    }

    function downloadCSV(csvContent: any, fileName: any) {
        const content = arrayToCSV(csvContent);
        const blob = new Blob([content], { type: 'text/csv' });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = fileName;
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
        URL.revokeObjectURL(url);
    }

    function generateCsv() {
        const headerArray = [
            'x',
            'y',
            'time_stamp',
            'speed',
            'pressure',
            'tiltX',
            'tiltY',
            'twist',
        ];

        const data = [
            headerArray,
            ...signatureData,
        ];

        downloadCSV(data, 'signature.csv');
    }
</script>

<div class="container">
    <div class="sign_here">Sign here</div>
    <div class="buttons_container">
        <button on:click={clearCanvas}>clear canvas</button>
        <button on:click={generateCsv}>generate csv</button>
    </div>
    <canvas width="800" height="400">
        Your browser does not support the HTML5 canvas element.
    </canvas>
    <div class="information_container">
        <div class="x_container">
            x: <span class="x_value" contenteditable="plaintext-only" bind:innerText={x}>0</span>
        </div>
        <div class="y_container">
            y: <span class="y_value" contenteditable="plaintext-only" bind:innerText={y}>0</span>
        </div>
        <div class="time_container">
            time: <span class="time_value" contenteditable="plaintext-only" bind:innerText={time}>0</span>
        </div>
        <div class="speed_container">
            speed: <span class="speed_value" contenteditable="plaintext-only" bind:innerText={speed}>0</span>
        </div>
        <div class="pressure_container">
            pressure: <span class="pressure_value" contenteditable="plaintext-only" bind:innerText={pressure}>0</span>
        </div>
        <div class="tiltX_container">
            tiltX: <span class="tiltX_value" contenteditable="plaintext-only" bind:innerText={tiltX}>0</span>
        </div>
        <div class="tiltY_container">
            tiltY: <span class="tiltY_value" contenteditable="plaintext-only" bind:innerText={tiltY}>0</span>
        </div>
        <div class="twist_container">
            twist: <span class="twist_value" contenteditable="plaintext-only" bind:innerText={twist}>0</span>
        </div>
    </div>
</div>

<style>
    .container {
        display: flex;
        gap: 10px;
        align-items: center;
        justify-content: center;
        flex-direction: column;
        height: 100%;
        width: 100%;
    }

    .sign_here {
        font-weight: bold;
        font-size: 50px;
        margin: 30px 0;
    }

    canvas {
        border: 1px solid #000;
        cursor: crosshair;
    }

    .information_container {
        display: flex;
        flex-wrap: wrap;
        width: 100%;
        max-width: 750px;
    }

    .information_container > * {
        background-color: rgba(0, 0, 0, 0.1);
        padding: 5px;
        margin: 5px;
        flex: 1;
        font-size: larger;
        min-width: 150px;
    }
</style>
