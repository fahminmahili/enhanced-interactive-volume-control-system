<script>
    let previousValue = 100; // Store the previous slider value

    let sliderValue = 0;

    // Function to calculate the reversed color gradient
    function calculateColor(value) {
        const hue = 240 - (value / 100) * 240; // blue to red
        return `hsl(${hue}, 50%, 60%)`;
    }

    // Function to generate the gradient for the slider background
    function generateGradient(value) {
        let gradientStops = [];
        for (let i = 0; i <= value; i += 1) {
            const color = calculateColor(i);
            const position = `${i}%`;
            gradientStops.push(`${color} ${position}`);
        }
        gradientStops.push(`#d3d3d3 ${value}%`);
        return `linear-gradient(to right, ${gradientStops.join(", ")})`;
    }

    // Function to calculate tooltip position
    function calculateTooltipPosition(value) {
        const trackWidth = 535;
        const thumbWidth = 50;
        const tooltipWidth = 40;
        const offset = (value / 100) * (trackWidth - thumbWidth) + thumbWidth / 2 - tooltipWidth / 2;
        return offset;
    }


    // Function to get the sound icon based on slider value
    function getSoundIcon(value) {
        if (value === 0) return "🔇"; // Muted
        if (value <= 33) return "🔈"; // Low
        if (value <= 66) return "🔉"; // Medium
        return "🔊"; // High
    }

    function muteSound() {
    if (sliderValue === 0) {
        // Restore the previous value
        sliderValue = previousValue;
    } else {
        // Save the current value and mute
        previousValue = sliderValue;
        sliderValue = 0;
    }
}

</script>

<style>
    .slider-container {
        display: flex;
        align-items: center;
        justify-content: center;
        height: 100vh;
        background-color: #f8f8f8;
    }

    .slider-wrapper {
        display: flex;
        align-items: center;
        gap: 15px;
        width: 600px;
        position: relative;
    }

    .slider {
        -webkit-appearance: none;
        appearance: none;
        width: 100%;
        height: 50px;
        border-radius: 25px;
        background: var(--slider-gradient, #d3d3d3);
        outline: none;
        cursor: pointer;
    }

    .slider::-webkit-slider-thumb {
        -webkit-appearance: none;
        appearance: none;
        width: 50px;
        height: 50px;
        border-radius: 50%;
        background: white;
        border: 5px solid black;
        cursor: pointer;
        transition: transform 0.2s ease, box-shadow 0.2s ease;
    }

    .slider::-webkit-slider-thumb:hover {
        transform: scale(1.2);
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
    }

    .tooltip {
        position: absolute;
        top: -90px;
        left: 0;
        transform: translateX(-50%);
        font-size: 16px;
        font-weight: bold;
        color: white;
        width: 50px;
        height: 50px;
        background: var(--tooltip-color, #ff758c);
        display: flex;
        align-items: center;
        justify-content: center;
        border-radius: 50%; 
        box-shadow: 0 0 10px var(--tooltip-color, #ff758c);
        pointer-events: none;
        opacity: 1;
        transition: transform 0.3s ease, opacity 0.3s ease, box-shadow 0.3s ease, background-color 0.3s ease;
    }

    .sound-icon {
        font-size: 50px; 
        color: #555;
        margin-left: 15px; 
        margin-top: -10px; 
    }

    .volume-warning {
        color: red;
        font-size: 18px;
        font-weight: bold;
        text-align: center;
        margin-bottom: 10px;
        height: 24px; 
        animation: blink 1s infinite;
    }

    @keyframes blink {
        50% { opacity: 0; }
    }


</style>

<h1 style="text-align: center; margin-bottom: 20px;">
    Dynamic Volume Control Slider
</h1>

<!-- Always reserve space for the warning message -->
<p class="volume-warning" style="visibility: {sliderValue > 85 ? 'visible' : 'hidden'};">
    ⚠ Warning: High Volume!
</p>

<div class="slider-container">
    <div class="slider-wrapper">
        <div
            class="tooltip"
            style="left: {calculateTooltipPosition(sliderValue)}px; --tooltip-color: {calculateColor(sliderValue)}"
        >
            {sliderValue}
        </div>
        <!-- Slider -->
        <input
            type="range"
            min="0"
            max="100"
            bind:value={sliderValue}
            class="slider"
            style="--slider-gradient: {generateGradient(sliderValue)}"
        />
        <!-- Sound Icon -->
        <div
            class="sound-icon"
            role="button"
            tabindex="0"
            on:click={muteSound}
            on:keydown={(e) => e.key === 'Enter' || e.key === ' ' ? muteSound() : null}
        >
            {getSoundIcon(sliderValue)}
        </div>
      
    </div>
</div>
