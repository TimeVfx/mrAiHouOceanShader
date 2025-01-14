# mrAiHouOceanShader

This project derives from [mrAiVexShader](https://github.com/mruegenberg/mrAiVexShader) to render Houdini Ocean spectra directly in Arnold, without exporting displacement maps or similar.

## Prerequisites
- An installation of Houdini
- The right compiler version matching your Houdini. You can find this in `Help > About Houdini > Show Details > Build platform`.  
  For Linux, this would be GCC 6.3 in the case of Houdini 18.5 (though Clang/LLVM also appears to work fine),
  for Windows, you'll need to install [Visual Studio](http://code.visualstudio.com) (include the `Windows SDK` as well as the `Windows 10 C++ SDK`), for OS X you'll need Xcode. 
  For details, see the [Houdini HDK documentation](https://www.sidefx.com/docs/hdk/_h_d_k__intro__compiling.html#HDK_Intro_Compiling_Windows).

## Compilation/Installation
1. Get the Arnold SDK for your system with Arnold version from [SolidAngle](https://www.solidangle.com/arnold/download/) and put it in `deps`.
2. Adjust the path to match your Arnold SDK and Houdini versions in `compile.sh` / `compile.bat`  
  (depending on your system, you just need to modify the .bat (Windows) or .sh (Linux/OS X)).
3. Windows only: you might also have to modify the `MSVCDir` line in `compile.bat` to point to where you installed it  
   (by default, the system variable is used and if that is not defined, we assume Visual Studio Community Edition).
4. Run the script for your OS from the command line
5. Copy `ai_ocean_samplelayers.dll/.so/.dylib` from the `build` to the `/arnold/plugins` directory in your Arnold installation (or wherever your Arnold looks for shaders).  
   Do the Same with the `.mtd` file from the `src` folder (this provides the shader UI). 
6. Start Houdini with Arnold and have some fun. (It's theoretically even possible to use this without Houdini, e.g in Maya or C4D, but you will probably use that to author the ocean spectra anyway.)

# Usage

1. Cache your ocean spectra to disk with the regular Houdini workflow for this. 
2. In you `Arnold Shader Network` or `Arnold Material Builder`, create a new `Ai Ocean Samplelayers` node.
3. Connect that to the `displacement` slot on your `Material Output` node.

## Examples

See the [blog post](https://www.marcelruegenberg.com/blog/2020/12/13/houdini-arnold-ocean-spectra.html)

# Status and Limitations

## Limitations
- At the moment, the Arnold render still starts significantly slower than the Houdini version, probably due to differences in renderer architecture/displacement performance.
- No possibility to export `velocity` or `cuspdir`. This is due to Arnold shaders always having only one output.
- the _Anti-Alias Blur_ parameter is not supported. To achieve a similar result, just reduce the maximum subdivision iterations. 

## Future Plans

- possibly provide better support for cusp by creating a separate shading node which outputs the value (and gets it from the main shader via [message passing](https://docs.arnoldrenderer.com/api/arnold-6.0.3.1/group__ai__shader__message.html#details)

- create an auxiliary shader to output bump/normal maps for reducing the amount of subdivisions needed
