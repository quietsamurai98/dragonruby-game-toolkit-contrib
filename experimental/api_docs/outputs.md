## GTK::Outputs
### #sprites
A [GTK::FlatArray](#TODO_LINK_TO_API_DOCS) that contains [sprites](#sprite-primitives) to render at the end of the current tick.  
`#sprites` is emptied between ticks, so sprites pushed into it only get rendered on the tick they are added.  
Example usage:
```rb
def tick args
  # You can shovel sprite primitives into #sprites one at a time...
  args.outputs.sprites << [590, 320, 100, 80, "sprites/dragon-0.png", args.state.tick_count]
  
  # ...or you can shovel them in bulk, which is preferred.
  args.outputs.sprites << [
    {x: 790, y: 320, w: 100, h: 80, path: "sprites/dragon-0.png", flip_horizontally: true},
    [390, 320, 100, 80, "sprites/dragon-0.png"]
  ]
end
```
### #static_sprites
A [GTK::FlatArray](#TODO_LINK_TO_API_DOCS) that contains [sprites](#sprite-primitives) to render at the end of the current tick.  
`#static_sprites` *is **not*** emptied between ticks, so a sprite added on tick 0 will continue to render on each tick until it is removed.  
Example usage:
```rb
def tick args
  if args.state.tick_count.zero?
    args.state.spinning_dragon = {x: 590, y: 320, w: 100, h: 80, path: "sprites/dragon-0.png", angle: 0}
    
    # You can shovel sprite primitives into #sprites one at a time...
    args.outputs.static_sprites << args.state.spinning_dragon
    
    # ...or you can shovel them in bulk, which is preferred.
    args.outputs.static_sprites << [
      {x: 790, y: 320, w: 100, h: 80, path: "sprites/dragon-0.png", flip_horizontally: true},
      [390, 320, 100, 80, "sprites/dragon-0.png"]
    ]
  end
  args.state.spinning_dragon.angle = args.state.tick_count
end
```

## Sprite Primitives
### Properties
 * `x ||= 0`: The x position of the sprite's bottom left corner, measured in pixels from the left side of the screen.
 * `y ||= 0`: The y position of the sprite's bottom left corner, measured in pixels from the bottom of the screen.
 * `w ||= 0`: The width of the sprite, measured in pixels.
 * `h ||= 0`: The height of the sprite, measured in pixels.
 * `path ||= nil`: A `String` containing the file path of the image to display, or a `Symbol` associated with a render target or a pixel array. 
 If no value is supplied, the file path doesn't point to a valid image, or the symbol is not associated with a render target or pixel array, 
 DragonRuby will use an image of a black and white checkerboard with a red outline instead.
 * `angle ||= 0`: The amount of counter-clockwise rotation applied to the sprite, measured in degrees.
 * `a ||= 255`: The opacity of the sprite, with `0` being fully transparent, and `255` being fully opaque.
 * `r ||= 255`: The "redness" of the sprite, with `0` setting the red channel of each pixel to 0, and `255` keeping the original value.
 * `g ||= 255`: The "greenness" of the sprite, with `0` setting the green channel of each pixel to 0, and `255` keeping the original value.
 * `b ||= 255`: The "blueness" of the sprite, with `0` setting the blue channel of each pixel to 0, and `255` keeping the original value.
 * `flip_horizontally ||= false` If truthy, the sprite will be mirrored horizontally.
 * `flip_vertically ||= false` If truthy, the sprite will be mirrored vertically.
 * `tile_x ||= 0`- The x offset of the viewport on the original image (useful for tile sheets)
 * `tile_y ||= 0`- The y offset of the viewport on the original image (useful for tile sheets)
 * `tile_w ||= w`- The width of the viewport on the original image (useful for tile sheets, defaults to sprite width)
 * `tile_h ||= h`- The height of the viewport on the original image (useful for tile sheets, defaults to sprite height)
 * `angle_anchor_x ||= 0.5` - Used to determine the x coordinate of the point around which the sprite is rotated: `rotation_point.x = sprite.x + angle_anchor_x * sprite.w`
 * `angle_anchor_y ||= 0.5` - Used to determine the y coordinate of the point around which the sprite is rotated: `rotation_point.y = sprite.y + angle_anchor_y * sprite.h`

### Array representation
```rb
[
  x, y, w, h, path, 
  angle, a, 
  r, g, b,
  flip_horizontally, flip_vertically, 
  tile_x, tile_y, tile_w, tile_h,
  angle_anchor_x, angle_anchor_y, 
  source_x, source_y, source_w, source_h
]
```  
To use the default value of a property, supply a `nil` value.    
If the array would have a bunch of `nil` values at the end, you can omit them like so:
```rb
[
  590, 320, 100, 80, "sprites/dragon-0.png",
  nil, 127, # Use the default angle and make the sprite half transparent.
  255, 127, 0 # Leave the red channel as is, darken the green channel by half, and set the blue channel to 0
  # Use default values for everything else.
]
```

### Hash representation
```rb
{
  x: x, y: y, w: w, h: h, path: path,
  angle: angle, a: a, 
  r: r, g: g, b: b,
  flip_horizontally: flip_horizontally, flip_vertically: flip_vertically, 
  tile_x: tile_x, tile_y: tile_y, tile_w: tile_w, tile_h: tile_h,
  angle_anchor_x: angle_anchor_x, angle_anchor_y: angle_anchor_y, 
  source_x: source_x, source_y: source_y, source_w: source_w, source_h: source_h
}
```  
To use the default value of a property, simply omit the property from the hash.
```rb
{
  x: 590, y: 320, w: 100, h: 80, path: "sprites/dragon-0.png",
  a: 127, # Make the sprite half transparent
  r: 255, g: 127, b: 0 # Leave the red channel as is, darken the green channel by half, and set the blue channel to 0
}
```

### Object representation
TODO: [EXPLAIN](https://www.youtube.com/watch?v=JYqjcHYTQgQ)
```rb
class DuckTypedSprite
  attr_accessor :x, :y, :w, :h, :path, :angle, :a, :r, :g, :b, :flip_horizontally, :flip_vertically, 
    :tile_x, :tile_y, :tile_w, :tile_h, :angle_anchor_x, :angle_anchor_y, :source_x, :source_y, :source_w, :source_h
  
  def primitive_marker
    :sprite
  end
end

class DrawOverrideSprite
  def draw_override(ffi_draw)
    # Implement your draw_override logic here. 
    # See also: FFI::Draw documentation.
  end
  
  def primitive_marker
    :sprite
  end
end
```
