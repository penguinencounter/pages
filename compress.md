> written for Figura 0.1.x.  
> inspired by a post by Total in #creative-hellp on the Figura discord. Thanks!

# Making Avatars Smaller

There's a **100 KB limit** on your avatar. If you exceed this, you won't be able to upload it to the main backend.

**[Skip to the compression strategies](#make-it-smaller)**

> [!note]
> <details><summary>Example <b>technical details</b> box (click to expand)</summary>
>
> ----
> Okay, so it's actually 100 KiB (kibibytes); that's 102,400 bytes.
>
> Boxes like this contain **technical details** on how specific parts of Figura work.
> If you're curious about what's happening behind the scenes or just want more details on how things work,
> you can **click the _Technical details_ text to expand the box**. (All future boxes will be collapsed by default.)
>   
> **These boxes are not required** and most people don't need to read them. **Feel free to skip them!**
>
> </details>

## How big is the avatar?

### What's taking up space?

You can check how big your avatar is in the information box on the right side of the Figura menu. This is the number you need to keep track of.

<mark>\[TODO: PUT IMAGE HERE]</mark>

Opening your avatar folder and looking at the file sizes is _not_ a good way to judge what is taking up space in your avatar.

Instead, run `/figura debug` in-game to get a report of what's going on with your avatar, including, among other things, a **breakdown of how its space is used**.

You can then read the outputted file yourself (hint: scroll to the bottom), or upload it to Discord for someone else to analyze.

### Figura already does an okay job at reducing your avatar's size

That's right! Figura does its best to get rid of data that isn't necessary in **scripts and models**. That means a few things:

- Feel free to write as many comments in your scripts as you want! They won't count towards your file size.
- File sizes in Explorer for models and scripts are generally _way bigger_ than their actual size cost in-game.

However, the rest of the avatar's content is pretty much used as-is; that means that **textures** and **sounds**
generally take up most of the space in an avatar.

> [!note]
> <details><summary><b>Technical details</b></summary>
>
> ----
> So, there's a bit more to it than that. Beyond just stripping unnecessary information from avatars, Figura also
> runs your entire avatar through the _deflate_ compression algorithm as the final step in producing a compiled avatar.
>
> You shouldn't really worry about optimizing your avatar to play well with this final compression layer; just think of
> it as free extra space. The only time this really plays a major part in compression is when you have multiple copies of
> the same asset in your avatar, where it can help mitigate the cost of the duplicated asset. Of course, duplicating assets
> _is not free_; you should avoid duplication whenever possible.
>
> </details>

**The main way to reduce the size of your avatar is to reduce the compacted size of its components.**

Remember that **there is no magic solution.** If your avatar is 1 MiB, there is likely nothing you can do to cut your avatar's size by 1/10 without giving up some of your avatar's content.

## MAKE IT SMALLER

### Scripts

- If scripts are _really_ the problem, you can adjust the **Format Script** setting in Figura's settings to change how
  aggressively it tries to compact your scripts.
    - **None** does nothing to your scripts. There is very little reason to use this option.
    - **Light** is the default and recommended option for most players. It deletes comments and other inconsequential things.
    - **Heavy** removes all the line breaks. This is not much of an improvement over Light, and also makes it significantly harder to fix problems with your avatar
    - **AST** tries to compact your scripts by changing the names of things. Some scripts can break with this, but it does make a significant difference compared to Light or Heavy.
- Removing scripts you don't care about as much is always an option.
- Reduce the amount of duplicated code you have in your script files. (You can ask for help with this!)

### Models

- **Avoid meshes**. Meshes use _way_ more data (~4x) than cubes to store. Also, if you're importing a hi-poly model from Blender or Roblox or something, please reconsider?
- If you want to keep using meshes, your priorities should be **reducing the number of vertices** and **removing unnecessary faces**. If you can't see a face in normal gameplay, it doesn't need to exist! Get rid of it!
    - Note that this advice does not apply to cubes.
- If you have multiple variants of your model, try to see if you can accomplish this by switching _textures_ or smaller parts of your model, rather than having multiple redundtant copies of parts.

> [!note]
> <details><summary><b>Technical details</b> (extras)</summary>
>
> ----
> If you're up for it, here's some extra stuff you can do:
> - For meshes, **use quads over tris** when possible. Tris are converted to quads by Figura, so dividing a quad into two tris
>   just uses more space for the same result.
> - Try to keep things at the default values. Figura can delete values that are set to their defaults, which saves a few bytes of space each time.
>
> </details>

### Textures

- Use a **texture compressor** to reduce the size of your textures, like [PNGGauntlet (Windows)](https://pnggauntlet.com/) or [oxipng](https://github.com/oxipng/oxipng)
- Ensure that your textures are in your avatar folder and properly **linked to your model files**.
    - If the textures are not in the avatar folder, Figura will use the copy of the texture that is embedded in the Blockbench file, which is ususally not compressed as well.
    - You can do this by right-clicking the texture in Blockbench, choosing **Change File**, and re-selecting the texture.
- **Reduce the number of colors** in your textures! Textures with less colors compress better.
    - Gradients and noise use up _loads_ of colors and thus do not compress well. Try to avoid them if possible.
- Remove empty space from your textures. Figura doesn't care if your texture is a rectangle.
- If you have many color variations on the same texture, consider making a single grayscale texture and tinting it with a script instead.
    - Do note that these tend to look 'washed out' since scripts can't do hue shifting by default.
    - For a _WAY more complicated_ but better-looking solution, expand the box below.


> [!note]
> <details><summary><b>More compression strategies</b></summary>
>
> ----
> - [oxipng](https://github.com/oxipng/oxipng) at high effort can apparently do better than PNGGauntlet? You can run it in your browser at in [squoosh.app](https://squoosh.app), just make sure you select OxiPNG and don't accidentally make a JPEG or something
> - If you can get your image below **256 colors**, compression apps like oxipng and PNGGauntlet can compress it much better! This is because they can use _(8bpp)_ [indexed color](https://en.wikipedia.org/wiki/Indexed_color), which is much more compact.
>     - The next big milestone for indexed color is **16 colors** _(4bpp)_.
> - Images with indexed colors (<256 colors) can be palette-swapped with the [colormagic](<https://github.com/penguinencounter/colormagic>) library (#selfpromo), which lets you work with hue-shifted palettes. This is really not recommended for newer Figura users. Feel free to ping penguinencounter for help setting this up.
>     - or just wait for 0.1.7 where this functionality is built in
>     - You can also **manually create palette swaps**. If you make sure the palette is laid out the same way on each of them, the aformentioned final DEFLATE pass will take care of most of the overhead! This is quite difficult to pull off without the right software, however (I know [GIMP](https://www.gimp.org/) can do it, but the learning curve for that is a cliff)
>
> </details>

### Sounds

- Consider **using Minecraft's built-in sounds** instead of custom sounds. They're free!
    - Play with sounds & pitches in your browser: https://misode.github.io/sounds/
    - But... if you're using a sound that has some randomized variations, you _can't choose which variation you get_.
- **Try [OptiVorbis](https://optivorbis.github.io/OptiVorbis/)**.
- Reduce the quality with audio editing software, like Audacity
    - I've made a [tool to do this online](https://penguinencounter.github.io/fumux-prod/prod/?builtin=figura), but you get less control this way

### General

- TODO
- could this be multiple avatars?
- host-only `/data` streaming? too complicated?
- TODO

> [!note]
> <details><summary><b>Technical details</b>: Reduced-precision numbers</summary>
>
> ----
> The post this article was based on recommended truncating numbers (reducing the number of digits) in models and scripts to save space. **In general, this advice is only useful for scripts.**
> 
> Most numbers in a model file are stored in 32 or 64 bits _regardless of how many digits they have_. The only exception is in fields that also have text in them (ex. instruction keyframes.)
>
> </details>
