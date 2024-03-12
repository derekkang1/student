---
toc: true
comments: false
layout: post
title: Sprite Animation
description: This is a sprite shet animation
type: hacks
courses: { compsci: {week: 5} }
---

<body>
    <div>
        <canvas id="spriteContainer"> <!-- Within the base div is a canvas. An HTML canvas is used only for graphics. It allows the user to access some basic functions related to the image created on the canvas (including animation) -->
            <img id="dogSprite" src="/student2/images/spritesheet.png">  // change sprite here
        </canvas>
        <div id="controls"> <!--basic radio buttons which can be used to check whether each individual animaiton works -->
            <input type="radio" name="animation" id="front" checked>
            <label for="front">Front</label><br>
            <input type="radio" name="animation" id="left">
            <label for="left">Left</label><br>
            <input type="radio" name="animation" id="right">
            <label for="right">Right</label><br>
            <input type="radio" name="animation" id="back">
            <label for="back">Back</label><br>
        </div>
    </div>
</body>

<script>
    // start on page load
    window.addEventListener('load', function () {
        const canvas = document.getElementById('spriteContainer');
        const ctx = canvas.getContext('2d');
        const SPRITE_WIDTH = 101.85;  // matches sprite pixel width
        const SPRITE_HEIGHT = 101.75; // matches sprite pixel height
        const SCALE_FACTOR = 2;  // control size of sprite on canvas
        const FRAME_LIMIT = 6;  // number of frames per row, this code assume each row is same
        // const FRAME_RATE = 15;  // not used

        canvas.width = SPRITE_WIDTH * SCALE_FACTOR;
        canvas.height = SPRITE_HEIGHT * SCALE_FACTOR;

        class Dog {
            constructor() {
                this.image = document.getElementById("dogSprite");
                this.spriteWidth = SPRITE_WIDTH;
                this.spriteHeight = SPRITE_HEIGHT;
                this.width = this.spriteWidth;
                this.height = this.spriteHeight;
                this.x = 0;
                this.y = 0;
                this.scale = SCALE_FACTOR;
                this.minFrame = 0;
                this.maxFrame = FRAME_LIMIT;
                this.frameX = 0;
                this.frameY = 0;
            }

            // draw dog object
            draw(context) {
                context.drawImage(
                    this.image,
                    this.frameX * this.spriteWidth,
                    this.frameY * this.spriteHeight,
                    this.spriteWidth,
                    this.spriteHeight,
                    this.x,
                    this.y,
                    this.width * this.scale,
                    this.height * this.scale
                );
            }

            // update frameX of object
            update() {
                if (this.frameX < this.maxFrame) {
                    this.frameX++;
                } else {
                    this.frameX = 0;
                }
            }
        }

        // dog object
        const dog = new Dog();

        // update frameY of dog object, action from idle, bark, walk radio control
        const controls = document.getElementById('controls');
        controls.addEventListener('click', function (event) {
            if (event.target.tagName === 'INPUT') {
                const selectedAnimation = event.target.id;
                switch (selectedAnimation) {
                    case 'front':
                        dog.frameY = 0;
                        break;
                    case 'left':
                        dog.frameY = 1;
                        break;
                    case 'right':
                        dog.frameY = 2;
                        break;
                    case 'back':
                        dog.frameY = 3;
                        break;
                    default:
                        break;
                }
            }
        });

// Animation recursive control function
    function animate() {
        // Clears the canvas to remove the previous frame.
        ctx.clearRect(0, 0, canvas.width, canvas.height);

        // Draws the current frame of the sprite.
        dog.draw(ctx);

    // Updates the `frameX` property to prepare for the next frame in the sprite sheet.
        dog.update();

    // Use setTimeout to introduce a delay before the next frame
        setTimeout(function() {
        // Uses `requestAnimationFrame` to synchronize the animation loop with the display's refresh rate,
        // ensuring smooth visuals. Call `animate` again to continue the animation loop.
            requestAnimationFrame(animate);
        }, 100); // Set the timeout delay in milliseconds (e.g., 100ms = 0.1 second)
    }

// Start the animation loop
animate();

        // run 1st animate
        animate();
    });
</script>>