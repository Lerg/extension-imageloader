# extension-imageloader

Image loader extension for Defold.

Loads JPG, PNG and other images efficiently into Defold's Buffer object.

It can load images in async and sync modes. And it can only process the header of an image to provide just width, height and channels information without fully decoding the image.

The extension uses `stb_image.h` under the hood.

# Supported platforms

Android, iOS, macOS, Linux, Windows, HTML5.

# URL for Defold
https://github.com/Lerg/extension-imageloader/archive/master.zip

## Syntax

```lua
imageloader.load(params)
```

### Params table

`data` - string, load image from string.

`filename` - string, load from file. Only if `data` is not set.

`channels` - number, force the provided amount of channels (1, 3 or 4). Default is 0 - automatic.

`info` - boolean, if `true`, don't decode the entire image, just return it's information. Default is `false`.

`no_vertical_flip` - boolean, if `true`, don't flip the image under the hood for compatibility with Defold. Default is `false`.

`no_async` - boolean, if `true`, perform sync call with a listener. Default is `false`.

`listener` - function, if `listener` is set, the loading is async. Listener receives two arguments: `self` - script instance and `image_resource` table.

### Return value

If the loading is performed without a listener, the function returns `image_resource` table, `nil` otherwise.

### `image_resource` table

- `header` - table, image information header.
	- `width` - number, width of the loaded image.
	- `height` - number, height of the loaded image.
	- `channels` - number, actual channel count in the image.
	- `format` - number, one of `resource.TEXTURE_FORMAT_LUMINANCE`, `resource.TEXTURE_FORMAT_RGB` or `resource.TEXTURE_FORMAT_RGBA`.
	- `type`, always `resource.TEXTURE_TYPE_2D`.
	- `num_mip_maps` - number, always 1.
- `buffer` - buffer, pixel data buffer object.

# Examples

## Sync load

```lua
local data = sys.load_resource('/res/image.jpg')

local image_resource = imageloader.load{
	data = data
}

pprint(image_resource)
```

## Async load

```lua
local data = sys.load_resource('/res/image.jpg')

imageloader.load{
	data = data,
	listener = function(self, image_resource)
		pprint(image_resource)
	end
}
```

## Info only

```lua
local data = sys.load_resource('/res/image.jpg')

local image_resource = imageloader.load{
	data = data,
	info = true
}

pprint(image_resource)
```

## Load from a file and convert to grayscale

```lua
local filename = directories.path_for_file('image.jpg', directories.documents)

local image_resource = imageloader.load{
	filename = filename,
	channels = 1
}

pprint(image_resource)
```

## Change texture of a model

```lua
local data = sys.load_resource('/res/image.jpg')

local image_resource = imageloader.load{
	data = data
}

resource.set_texture(go.get('#model', 'texture0'), image_resource.header, image_resource.buffer)
```
