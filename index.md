---
layout: base
title: Course Outlines
image: /images/mario_animation.png
hide: true
---

<!-- Liquid:  statements -->

<!-- Include submenu from _includes to top of pages -->
{% include nav_home.html %}
<!--- Concatenation of site URL to frontmatter image  --->
{% assign sprite_file = site.baseurl | append: page.image %}
<!--- Has is a list variable containing mario metadata for sprite --->
{% assign hash = site.data.mario_metadata %}  
<!--- Size width/height of Sprit images --->
{% assign pixels = 256 %} 

<!--- HTML for page contains <p> tag named "Mario" and class properties for a "sprite"  -->

<p id="mario" class="sprite"></p>
  
<!--- Embedded Cascading Style Sheet (CSS) rules, 
        define how HTML elements look 
--->
<style>

  /*CSS style rules for the id and class of the sprite...
  */
  .sprite {
    height: {{pixels}}px;
    width: {{pixels}}px;
    background-image: url('{{sprite_file}}');
    background-repeat: no-repeat;
  }

  /*background position of sprite element
  */
  #mario {
    background-position: calc({{animations[0].col}} * {{pixels}} * -1px) calc({{animations[0].row}} * {{pixels}}* -1px);
  }
</style>

<!--- Embedded executable code--->
<script>
  ////////// convert YML hash to javascript key:value objects /////////

  var mario_metadata = {}; //key, value object
  {% for key in hash %}  
  
  var key = "{{key | first}}"  //key
  var values = {} //values object
  values["row"] = {{key.row}}
  values["col"] = {{key.col}}
  values["frames"] = {{key.frames}}
  mario_metadata[key] = values; //key with values added

  {% endfor %}

  ////////// game object for player /////////

  class Mario {
    constructor(meta_data) {
      this.tID = null;  //capture setInterval() task ID
      this.positionX = 0;  // current position of sprite in X direction
      this.currentSpeed = 0;
      this.marioElement = document.getElementById("mario"); //HTML element of sprite
      this.pixels = {{pixels}}; //pixel offset of images in the sprite, set by liquid constant
      this.interval = 100; //animation time interval
      this.obj = meta_data;
      this.direction = "right";
      this.marioElement.style.position = "absolute";
    }

    animate(obj, speed) {
      let frame = 0;
      const row = obj.row * this.pixels;
      this.currentSpeed = speed;

      this.tID = setInterval(() => {
        const col = (frame + obj.col) * this.pixels;
        this.marioElement.style.backgroundPosition = `-${col}px -${row}px`;
        this.marioElement.style.left = `${this.positionX}px`;

        this.positionX += speed;
        frame = (frame + 1) % obj.frames;

        const viewportWidth = window.innerWidth;
        if (this.positionX > viewportWidth - this.pixels) {
          document.documentElement.scrollLeft = this.positionX - viewportWidth + this.pixels;
        }
      }, this.interval);
    }

    startWalking() {
      this.stopAnimate();
      if(this.direction == "right"){
        this.animate(this.obj["Walk"], 3);
      }else if(this.direction == "left"){
        this.animate(this.obj["WalkL"], -3);
      }
    }

    startRunning() {
      this.stopAnimate();
      if(this.direction == "right"){
        this.animate(this.obj["Run1"], 6);
      }else if(this.direction == "left"){
        this.animate(this.obj["Run1L"], -6);
      }
    }

    startPuffing() {
      this.stopAnimate();
      this.animate(this.obj["Puff"], 0);
    }

    startCheering() {
      this.stopAnimate();
      if(this.direction == "right"){
        this.animate(this.obj["Cheer"], 0);
      }else if(this.direction == "left"){
        this.animate(this.obj["CheerL"], 0);
      }

      
    }

    startFlipping() {
      this.stopAnimate();
      this.animate(this.obj["Flip"], 0);
    }

    startResting() {
      this.stopAnimate();
      this.animate(this.obj["Rest"], 0);
    }

    stopAnimate() {
      clearInterval(this.tID);
    }
  }

  const mario = new Mario(mario_metadata);

  ////////// event control /////////

  window.addEventListener("keydown", (event) => {
    if (event.key === "ArrowRight") {
      event.preventDefault();
      if (event.repeat) {
        mario.startCheering();
      } else {
        if (mario.currentSpeed === 0) {
          mario.direction = "right";
          mario.startWalking();
        } else if (mario.currentSpeed === 3) {
          mario.startRunning();
        } else if (mario.currentSpeed === 6) {
          mario.startResting();
        }
      }
    } else if (event.key === "ArrowLeft") {
      event.preventDefault();
      if (event.repeat) {
        mario.startCheering();
      } else {
        if (mario.currentSpeed === 0) {
          mario.direction = "left";
          mario.startWalking();
        } else if (mario.currentSpeed === -3) {
          mario.startRunning();
        } else if (mario.currentSpeed === -6) {
          mario.startResting();
        }
      }
    }
  });

  //touch events that enable animations
  window.addEventListener("touchstart", (event) => {
    event.preventDefault(); // prevent default browser action
    if (event.touches[0].clientX > window.innerWidth / 2) {
      // move right
      if (currentSpeed === 0) { // if at rest, go to walking
        mario.startWalking();
      } else if (currentSpeed === 3) { // if walking, go to running
        mario.startRunning();
      }
    } else {
      // move left
      mario.startPuffing();
    }
  });

  //stop animation on window blur
  window.addEventListener("blur", () => {
    mario.stopAnimate();
  });

  //start animation on window focus
  window.addEventListener("focus", () => {
     mario.startFlipping();
  });

  //start animation on page load or page refresh
  document.addEventListener("DOMContentLoaded", () => {
    // adjust sprite size for high pixel density devices
    const scale = window.devicePixelRatio;
    const sprite = document.querySelector(".sprite");
    sprite.style.transform = `scale(${0.2 * scale})`;
    mario.startResting();
  });

</script>

## CSSE Home Page -Derek Kang
Welcome to my blog page.
<body>
    <div>
        <canvas id="spriteContainer">
            <img id="box" src="{{site.baseurl}}/images/box.png"> 
        </canvas>
    </div>
</body>

<script>

    window.addEventListener('load', function () {
        const canvas = document.getElementById('spriteContainer');
        const ctx = canvas.getContext('2d');
        const SPRITE_WIDTH = 71.75;
        const SPRITE_HEIGHT = 82.5;
        const SCALE_FACTOR = 2;
        const DESIRED_FRAME_RATE = 15;
        const FRAME_INTERVAL = 1000 / DESIRED_FRAME_RATE;
        canvas.width = SPRITE_WIDTH * SCALE_FACTOR * 7;
        canvas.height = SPRITE_HEIGHT * SCALE_FACTOR;

        class Box {
            constructor() {
                this.image = document.getElementById("box");
                this.spriteWidth = SPRITE_WIDTH;
                this.spriteHeight = SPRITE_HEIGHT;
                this.width = this.spriteWidth;
                this.height = this.spriteHeight;
                this.x = 0;
                this.y = 0;
                this.scale = SCALE_FACTOR;
                this.minFrame = 0;
                this.frameY = 0;
                this.frameX = 0;
                this.maxFrame = 7;
                this.speed = 10; 
            }
            setFrameLimit(limit) {
                this.maxFrame = limit;
            }
            setPosition(x, y) {
                this.x = x;
                this.y = y;
            }
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
            update() {
                if (this.frameX < this.maxFrame) {
                    this.frameX++;
                } else {
                    this.frameX = 0;
                }
            }
        }

        const box = new Box();

        const keyState = {
            ArrowLeft: false,
            ArrowRight: false,
            ArrowUp: false,
        };

        document.addEventListener('keydown', function (event) {
            switch (event.key) {
                case 'a':
                    keyState.ArrowLeft = true;
                    break;
                case 'd':
                    keyState.ArrowRight = true;
                    break;
                case 'w':
                    keyState.ArrowUp = true;
                    break;
            }
        });

        document.addEventListener('keyup', function (event) {
            switch (event.key) {
                case 'a':
                    keyState.ArrowLeft = false;
                    break;
                case 'd':
                    keyState.ArrowRight = false;
                    break;
                case 'w':
                    keyState.ArrowUp = false;
                    break;
            }
        });

        function updateAnimations() {
            let selectedAnimation = 'A';
            box.frameY = 0;
            if (keyState.ArrowLeft) {
                box.x -= box.speed;
            }
            if (keyState.ArrowRight) {
                box.x += box.speed;
            }
            if (keyState.ArrowUp) {
                selectedAnimation = 'B';
                box.frameY = 1;
            } 
        }

        let lastTimestamp = 0;
        function animate(timestamp) {
            const deltaTime = timestamp - lastTimestamp;
            if (deltaTime >= FRAME_INTERVAL) {
                ctx.clearRect(0, 0, canvas.width, canvas.height);
                box.draw(ctx);
                box.update();
                updateAnimations();
                lastTimestamp = timestamp;
            }
            requestAnimationFrame(animate);
        }

        animate();
    });
</script>

## About Me
Hi, I'm Derek Kang. I'm currently 14 years old and a freshmen at DNHS, Class of 2027. My academic interests are math and science, particularly physics and chemistry. In my free time I like to play Brawl Stars and hang out with my friends. My hobbies include playing violin, playing tennis, biking, snowboarding, and robotics. I can speak chinese, as well as english. My favorite food is pasta.

<img src="{{site.baseurl}}/images/Untitled presentation.png">