# Post-effects for developers

This diagram shows the ImageEffects interfaces and implementation classes used to develop the post-processing effects:

- The interface @'SiliconStudio.Xenko.Rendering.Images.IImageEffect' is the root interface for implementing an arbitrary image effect.
- The interface @'SiliconStudio.Xenko.Rendering.IImageEffectRenderer' is the interface for implementing a post-effect that can be instantiated from the editor.
  - For example, @'SiliconStudio.Xenko.Rendering.Images.PostProcessingEffects' and @'SiliconStudio.Xenko.Rendering.Images.GaussianBlur' inherit from this interface and are accessible from the editor.
- The @'SiliconStudio.Xenko.Rendering.Images.ImageEffect' is a base implementation of the @'SiliconStudio.Xenko.Rendering.Images.IImageEffect'. We recommend you derive from this class to develop custom effects.

![media/post-effects-for-developers-1.png](media/post-effects-for-developers-1.png) 

## See also

* [Bloom](bloom.md)
* [Bright filter](bright-filter.md)
* [color transforms](color-transforms/index.md)
* [Depth of field](depth-of-field.md)
* [Lens flare](lens-flare.md)
* [Light streaks](light-streaks.md)