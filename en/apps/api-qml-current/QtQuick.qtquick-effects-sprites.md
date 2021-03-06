---
Title: QtQuick.qtquick-effects-sprites
---

# QtQuick.qtquick-effects-sprites

<span class="subtitle"></span>
<!-- $$$qtquick-effects-sprites.html-description -->
<h2 id="sprite-engine">Sprite Engine</h2>
<p>The <a href="QtQuick.qtquick-index.md">Qt Quick</a> sprite engine is a stochastic state machine combined with the ability to chop up images containing multiple frames of an animation.</p>
<h3 >State Machine</h3>
<p>A primary function of the sprite engine is its internal state machine. This is not the same as the states and transitions in Qt Quick, and is more like a conventional state machine. Sprites can have weighted transitions to other sprites, or back to themselves. When a sprite animation finishes, the sprite engine will choose the next sprite randomly, based on the weighted transitions available for the sprite that just finished.</p>
<p>You can affect the currently playing sprite in two ways. You can arbitrarily force it to immediately start playing any sprite, or you can tell it to gradually transition to a given sprite. If you instruct it to gradually transition, then it will reach the target sprite by going through valid state transitions using the fewest number of intervening sprites (but ignoring relative weightings). This allows you to easily insert a transitional animation between two different sprites.</p>
<p class="centerAlign"><img src="../../../media/spriteenginegraph.png" alt="" /></p><p>As an example, consider the above diagram which illustrates the sprites for a hypothetical 2D platform game character. The character starts by displaying the standing state. From this state, barring external input, he will transition to either the waiting animation, the walking animation, or play the standing animation again. Because the weights for those transitions are one, zero and three respectively, he has a one in four chance of playing the waiting animation when the standing animation finishes, and a three in four chance of playing the standing animation again. This allows for a character who has a slightly animated and variable behavior while waiting.</p>
<p>Because there is a zero weight transition to the walking animation, the standing animation will not normally transition there. But if you set the goal animation to be the walking animation, it would play the walking animation when it finished the standing animation. If it was previously in the waiting animation, it would finish playing that, then play the standing animation, then play the walking animation. It would then continue to play the walking animation until the goal animation is unset, at which point it would switch to the standing animation after finishing the walking animation.</p>
<p>If you set the goal state then to the jumping animation, it would finish the walking animation before playing the jumping animation. Because the jumping animation does not transition to other states, it will still keep playing the jumping animation until the state is forced to change. In this example, you could set it back to walking and change to goal animation to walking or to nothing (which would lead it to play the standing animation after the walking animation). Note that by forcibly setting the animation, you can start playing the animation immediately.</p>
<h3 >Input Format</h3>
<p>The file formats accepted by the sprite engine is the same as the file formats accepted by other QML types, such as <a href="QtQuick.qtquick-imageelements-example.md#image">Image</a>. In order to animate the image however, the sprite engine requires the image file to contain all of the frames of the animation. They should be arranged in a contiguous line, which may wrap from the right edge of the file to a lower row starting from the left edge of the file (and which is placed directly below the previous row).</p>
<p class="centerAlign"><img src="../../../media/spritecutting.png" alt="" /></p><p>As an example, take the above image. For now just consider the black numbers, and assume the squares are 40x40 pixels. Normally, the image is read from the top-left corner. If you specified the frame size as 40x40 pixels, and a frame count of 8, then it would read in the frames as they are numbered. The frame in the top left would be the first frame, the frame in the top right would be the fifth frame, and then it would wrap to the next row (at pixel location 0,40 in the file) to read the sixth frame. It would stop reading after the frame marked 8, and if there was any image data in the square below frame four then it would not be included in the animation.</p>
<p>It is possible to load animations from an arbitrary offset, but they will still follow the same pattern. Consider now the red numbers. If we specify that the animation begins at pixel location 120,0, with a frame count of 5 and the same frame size as before, then it will load the frames as they are numbered in red. The first 120x40 of the image will not be used, as it starts reading 40x40 blocks from the location of 120,0. When it reaches the end of the file at 160,0, it then starts to read the next row from 0,40.</p>
<p>The blue numbers show the frame numbers if you tried to load two frames of that size, starting from 40,40. Note that it is possible to load multiple sprites out of the one image file. The red, blue and black numbers can all be loaded as separate animations to the same sprite engine. The following code loads the animations as per the image. It also specifies that animations are to played at 20 frames per second.</p>
<pre class="cpp">Sprite {
name: <span class="string">&quot;black&quot;</span>
source: <span class="string">&quot;image.png&quot;</span>
frameCount: <span class="number">8</span>
frameWidth: <span class="number">40</span>
frameHeight: <span class="number">40</span>
frameRate: <span class="number">20</span>
}
Sprite {
name: <span class="string">&quot;red&quot;</span>
source: <span class="string">&quot;image.png&quot;</span>
frameX: <span class="number">120</span>
frameCount: <span class="number">5</span>
frameWidth: <span class="number">40</span>
frameHeight: <span class="number">40</span>
frameRate: <span class="number">20</span>
}
Sprite {
name: <span class="string">&quot;blue&quot;</span>
source: <span class="string">&quot;image.png&quot;</span>
frameX: <span class="number">40</span>
frameX: <span class="number">40</span>
frameCount: <span class="number">2</span>
frameWidth: <span class="number">40</span>
frameHeight: <span class="number">40</span>
frameRate: <span class="number">20</span>
}</pre>
<p>Frames within one animation must be the same size, however multiple animations within the same file do not. Sprites without a frameCount specified assume that they take the entire file, and you must specify the frame size. Sprites without a frame size assume that they are square and take the entire file without wrapping, and you must specify a frame count.</p>
<p>The sprite engine internally copies and cuts up images to fit in an easier to read internal format, which leads to some graphics memory limitations. Because it requires all the sprites for a single engine to be in the same texture, attempting to load many different animations can run into texture memory limits on embedded devices. In these situations, a warning will be output to the console containing the maximum texture size.</p>
<p>There are several software tools to help turn images into sprite sheets, here are some examples: Photoshop plugin: http://www.personal.psu.edu/zez1/blogs/my_blog/2011/05/scripts-4-photoshop-file-sequence-to-layers-to-sprite-sheet.html Gimp plugin: http://registry.gimp.org/node/20943 Cmd-line tool: http://www.imagemagick.org/script/montage.php</p>
<h3 >QML Types Using the Sprite Engine</h3>
<p>Sprites for the sprite engine can be defined using the <a href="QtQuick.Sprite.md">Sprite</a> type. This type includes the input parameters as well as the length of the animation and weighted transitions to other animations. It is purely a data class, and does not render anything.</p>
<p><a href="QtQuick.qtquick-imageelements-example.md#spritesequence">SpriteSequence</a> is a type which uses a sprite engine to draw the sprites defined in it. It is a single and self-contained sprite engine, and does not interact with other sprite engines. <a href="QtQuick.Sprite.md">Sprite</a> types can be shared between sprite engine using types, but this is not done automatically. So if you have defined a sprite in one <a href="QtQuick.qtquick-imageelements-example.md#spritesequence">SpriteSequence</a> you will need to redefine it (or reference the same <a href="QtQuick.Sprite.md">Sprite</a> type) in the sprites property of another <a href="QtQuick.qtquick-imageelements-example.md#spritesequence">SpriteSequence</a> in order to transition to that animation.</p>
<p>Additionally, <a href="QtQuick.Particles.ImageParticle.md">ImageParticle</a> can use <a href="QtQuick.Sprite.md">Sprite</a> types to define sprites for each particle. This is again a single sprite engine per type. This works similarly to <a href="QtQuick.qtquick-imageelements-example.md#spritesequence">SpriteSequence</a>, but it also has the parametrized variability provided by the <a href="QtQuick.Particles.ImageParticle.md">ImageParticle</a> type.</p>
<h2 id="animatedsprite">AnimatedSprite</h2>
<p>For use-cases which do not need to transition between animations, consider the <a href="#animatedsprite">AnimatedSprite</a> type. This type displays sprite animations with the same input format, but only one at a time. It also provides more fine-grained manual control, as there is no sprite engine managing the timing and transitions behind the scenes.</p>
<!-- @@@qtquick-effects-sprites.html -->
