<!---

This file is used to generate your project datasheet. Please fill in the information below and delete any unused
sections.

You can also include images in this folder and reference them in the markdown. Each image must be less than
512 kb in size, and the combined size of all images must be less than 1 MB.
-->

## How it works

This project generates a multi-layered, animated geometric pattern on a VGA display entirely in hardware. 

It works by tracking the current screen position using an internal `hvsync_generator` that outputs the current pixel coordinates (`pix_x` and `pix_y`). To animate the image, a `counter` register increments once per frame (on the positive edge of `vsync`). By adding multiples of this counter to the X and Y coordinates, we create offset coordinates that make each layer appear to move at different speeds and in different directions, simulating a parallax effect.

The shapes are mathematically generated using bitwise operations:
* **Checkerboards:** Created by XORing (`^`) specific bits of the X and Y coordinates. Higher bits create larger squares, while lower bits create smaller squares.
* **Diamonds:** Created by calculating the sum and difference of the X and Y coordinates, and then XORing those results to form diagonal intersecting patterns.
* **Transparency (Dithering):** Several layers achieve a semi-transparent look by masking the shape with a 1-pixel checkerboard pattern (using a bitwise AND `&` with the lowest coordinate bits). 

Finally, a priority multiplexer assigns 6-bit RGB colors to each layer. The new diamond layer is rendered on top in yellow, while the colors of the underlying layers dynamically react to the input pins (`ui_in`).

## How to test

To test the project, you need to connect the outputs to a VGA monitor using a standard resistor DAC (like the TinyVGA PMOD).

1. Connect a clock signal to the `clk` input (typically 25.175 MHz for a standard 640x480 @ 60Hz VGA signal, depending on the board setup).
2. Pulse the `rst_n` pin low, then set it high to initialize the animation counter.
3. Observe the VGA monitor: you should see multiple moving checkerboard layers and a prominent yellow diamond pattern scrolling across the screen.
4. **Interact:** Toggle the dedicated input switches (`ui_in[5:0]`). Because the color variables (`color_a`, `color_b`, etc.) are mathematically derived from `ui_in`, changing these switches will instantly alter the color palette of the background layers in real time.

## External hardware

* TinyVGA PMOD (or an equivalent resistor network VGA DAC connected to the output pins).
* VGA Monitor and cable.
