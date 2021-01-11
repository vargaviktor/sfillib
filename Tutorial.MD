# Tutorial for SFILLIB

## Introduction

SFILLIB is a Simple and Fast Image Loading library for the Nintendo 3DS (using sf2dlib and ctrulib) It is then easy usable form the sf2dlib, which is a simple abstraction layer over citro3d with extra functions over citro3d to make displaying simple shapes and 2d sprites easy.

## Usage
### Preparation
You need clone and build the library:
1. Clone the library
2. In the directory sfillib/libsfil/ run:
    ```
    make
    make install
    ```

### LIBS in make file
* You need to add the LIBS variable the ```-lsfil -lsf2d -lcitro3d```.
* If  you get LD errors check the dependencies also added in the rigth order (for example: ```LIBS	:= -lsfil -lpng -ljpeg -lz -lsf2d -lcitro3d -lctru -lm``` )

### Adding to include
* You should add it to the include section in your code: 
```c
#include <3ds.h>
#include <sf2d.h>
#include <sfil.h>
```

## Examining the sample file

Sample 3dsx available from [here](https://github.com/vargaviktor/sfillib/tree/master/sample) for test runs.

![Screenshot of sample](https://github.com/vargaviktor/sfillib/blob/master/sfillib_sample.png)

The code first starts with the standard includes, and also includes the pictures converted to .h with BIN2S.

```c
#include <3ds.h>
#include <sf2d.h>
#include <sfil.h>

#include "citra_jpeg.h"
#include "3dbrew_png.h"
```
In the main at first the sf2d initialization is done:
```c
int main()
{
	sf2d_init();
	sf2d_set_clear_color(RGBA8(0x40, 0x40, 0x40, 0xFF));
```
The the SFILLIB magic happens:
```c
	sf2d_texture *tex1 = sfil_load_JPEG_buffer(citra_jpeg, citra_jpeg_size, SF2D_PLACE_RAM);
	sf2d_texture *tex2 = sfil_load_PNG_buffer(_3dbrew_png, SF2D_PLACE_RAM);
```
These loads the files from the buffer into a texture, which can be displayed by the sf2d:\n
Parametering
* In the case of PNG and BMP files 2 parameters needed: the buffer and the sf2d place is needed.
* In the case of JPEG 3 parameters needed: the buffer, the buffer size and the sf2d place is needed.

Not in the example, but for the BMP the ```sf2d_texture* sfil_load_BMP_buffer``` is the right command, with the same parameters as in the case of PNG.

To load a file, you can respectly used the follwoing functions, where the filename is the filename with path.

```c
	sf2d_texture * 	sfil_load_PNG_file (const char *filename, sf2d_place place)
	sf2d_texture * 	sfil_load_JPEG_file (const char *filename, sf2d_place place)
	sf2d_texture * 	sfil_load_BMP_file (const char *filename, sf2d_place place)
```
The rest of the example is standard.
The keyboard is scanned, and exits, it START pressed. (after freeing the resources)
Otherwise starts a top screen frame, draws the first texture which was loaded from file, then the same with the bottom screen, then swaps the buffers.

```c
	while (aptMainLoop()) {

		hidScanInput();

		if (hidKeysHeld() & KEY_START) {
			break;
		}

		sf2d_start_frame(GFX_TOP, GFX_LEFT);

			sf2d_draw_texture(tex1, 400/2 - tex1->width/2, 240/2 - tex1->height/2);

		sf2d_end_frame();

		sf2d_start_frame(GFX_BOTTOM, GFX_LEFT);

			sf2d_draw_texture(tex2, 320/2 - tex2->width/2, 240/2 - tex2->height/2);

		sf2d_end_frame();

		sf2d_swapbuffers();
	}

	sf2d_free_texture(tex1);
	sf2d_free_texture(tex2);

	sf2d_fini();
	return 0;
}
```
