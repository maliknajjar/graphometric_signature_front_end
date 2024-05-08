<!-- DrawCanvas.svelte -->
<script lang="ts">
    import { onMount } from "svelte";

    onMount(() => {
        const canvas = document.getElementById('drawCanvas') as HTMLCanvasElement;
        const ctx = canvas.getContext('2d');

        let prev_x: number = 0;
        let prev_y: number = 0;
        let prev_time: number = 0;

        if (!ctx) {
            alert('Drawing Context is null')
            throw new Error("Context is null");
        }

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
            const x = event.clientX - canvas.offsetLeft;
            const y = event.clientY - canvas.offsetTop;
            const time = event.timeStamp;
            const distance = Math.sqrt(Math.pow(x - prev_x, 2) + Math.pow(y - prev_y, 2)); 
            const timeDiff = time - prev_time;
            const speed = distance / timeDiff;

            console.log('OOOOOOOOOOOOOOOOOOOOOOOO');
            console.log('x, y: ', x, y);
            console.log('distance: ', distance);
            console.log('prev_time: ', prev_time);
            console.log('time: ', time);
            console.log('time diff: ', timeDiff);
            console.log('speed: ', speed);
            console.log('pressure: ', event.pressure);
            console.log('tangentialPressure: ', event.tangentialPressure);
            console.log('tiltX: ', event.tiltX);
            console.log('tiltY: ', event.tiltY);
            console.log('twist: ', event.twist);
            console.log('pointer type: ', event.pointerType);
            console.log('time stamp: ', event.timeStamp);

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

        canvas.addEventListener('pointerup', function() {
            isDrawing = false;
        });

        canvas.addEventListener('pointerleave', function() {
            isDrawing = false;
        });
    });
</script>

<div class="container">
    <div class="sign_here">Sign here</div>
    <canvas id="drawCanvas" width="800" height="400">
        Your browser does not support the HTML5 canvas element.
    </canvas>
</div>

<style>
    .container {
        display: flex;
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
</style>
