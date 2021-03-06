# Effect language

## Create shaders in C&#35;

You can create a shader at runtime with @'SiliconStudio.Xenko.Shaders.ShaderSource' objects. Shaders come in three variations:

- @'SiliconStudio.Xenko.Shaders.ShaderClassSource' correspond to a unique class
- @'SiliconStudio.Xenko.Shaders.ShaderMixinSource' mix several @'SiliconStudio.Xenko.Shaders.ShaderSource', set preprocessor values, define compositions
- @'SiliconStudio.Xenko.Shaders.ShaderArraySource' are used for arrays of compositions

This method produces shaders at runtime. However, many platforms don't support HLSL and have no ability to compile shaders at runtime. Additionally, the approach doesn't benefit from the reusability of mixins.

## Xenko Effects (XKFX)

Many shaders are variations or combinations of pre-existing shaders. For example, some meshes cast shadows, others receive them, and others need skinning. To reuse code, it's a good idea to select which parts to use through conditions (eg "Skinning required"). This is often solved by "uber shaders": monolithic shaders configured by a set of preprocessor parameters.

Xenko offers the same kind of control, keeping extensibility and reusability in mind. The simple code blocks defined by XKSL classes can be mixed together by a shader mixer. This process can use more complex logic, described in Xenko Effect (*.XKFX) files.

### General syntax

A .XKFX file is a small program used to generate shader permutations. It takes a set of parameters (key and value in a collection) and produces a `ShaderMixinSource` ready to be compiled.

An XKFX example file:

```cs
using SiliconStudio.Xenko.Effects.Data;

namespace XenkoEffects
{
	params MyParameters
	{
		bool EnableSpecular = true;
	};
	
	shader BasicEffect
	{
		using params MaterialParameters;
		using params MyParameters;

		mixin ShaderBase;
		mixin TransformationWAndVP;
		mixin NormalVSStream;
		mixin PositionVSStream;
		mixin BRDFDiffuseBase;
		mixin BRDFSpecularBase;
		mixin LightMultiDirectionalShadingPerPixel<2>;
		mixin TransparentShading;
		mixin DiscardTransparent;

		if (MaterialParameters.AlbedoDiffuse != null)
		{
			mixin compose DiffuseColor = ComputeBRDFDiffuseLambert;
			mixin compose albedoDiffuse = MaterialParameters.AlbedoDiffuse;
		}

		if (MaterialParameters.AlbedoSpecular != null)
		{
			mixin compose SpecularColor = ComputeBRDFColorSpecularBlinnPhong;
			mixin compose albedoSpecular = MaterialParameters.AlbedoSpecular;
		}
	};
}
```

### Adding mixins

To add a mixin, simply use `mixin <mixin_name>`.

### Using parameters

The syntax is similar to C#. The following rules are added:

- When you use parameter keys, add the using `params <class_name>`. If you don't, keys will be treated as variables.
- You don't need to to tell the program where to check the values behind the keys. Just use the key.

#### Parameters

```cs
using params MaterialParameters;
 
if (MaterialParameters.AlbedoDiffuse != null)
{
	mixin MaterialParameters.AlbedoDiffuse;
}
```

The parameters behave like any variable. You can read and write their value, compare their values, and set template parameters. Since some parameters store mixins, they can be used for composition and inheritance, too.

### Custom parameters

You can create your own set of parameters using a structure definition syntax. Even if they're defined in the XKFX file, don't forget the `using` statement when you want to use them.

#### Custom parameters

```cs
params MyParameters
{
	bool EnableSpecular = true; // true is the default value
}
```

### Compositions

To add a composition, assign the composition variable to your mixin with the syntax below.

#### Compositions

```cs
// albedoSpecular is the name of the composition variable in the mixin
mixin compose albedoSpecular = ComputeColorTexture;
 
or
 
mixin compose albedoSpecular = MaterialParameters.AlbedoSpecular;
```

### Partial shaders

You can also break the code into sub-mixins to reuse elsewhere with the syntax below.

#### Partial shader

```cs
partial shader MyPartialShader
{
	mixin ComputeColorMultiply;
	mixin compose color1 = ComputeColorStream;
	mixin compose color2 = ComputeColorFixed;
}
 
// to use it
mixin MyPartialShader;
mixin compose myComposition = MyPartialShader;
```

You can use the `MyPartialShader` mixin like any other mixin in the code.