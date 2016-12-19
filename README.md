# LZF
> A simple C# LZF compression implementation which attempts to minimize memory allocations.

While LZF compression might not have the great compression ratio of some algorithms, it is simple and extremely fast. This makes it great for applications where speed and responsiveness takes priority over disk space, such as in game development. However, some implementations of LZF are a bit careless with memory allocations which could potentially cause some issues if you're on a platforms with limited memory and/or where you're trying to manage and minimize garbage collection.

## Usage

This implementation includes several compress/decompress methods which can be called. Several of these aren't geared towards minimizing memory allocation but are included primarily for legacy support with older implementations.

If you're looking to keep memory allocations as limited as possible, you'll want to use these methods:

```C#
public static int Compress(byte[] inputBytes, ref byte[] outputBuffer)
public static int Deompress(byte[] inputBytes, ref byte[] outputBuffer)
```

* `inputBytes`: Input bytes to compress/decompress.
* `outputBuffer`: A reference to the buffer where the output will be produced. This will be allocated/resized as necessary, so it's acceptable to initially pass in a null reference.
* `Returns`: The size of the output in bytes. This is **VERY IMPORTANT** to note because this will be smaller than outputBuffer.Length in most cases. A small inconvenience but necessary if you want to avoid unnecessary memory allocations.

Here's a quick example of using this library to compress some generic input data and write it to a file:

```C#
// Declare input/output buffers.
byte[] inputBuffer = null;
byte[] outputBuffer = null;

// Open output file.
using(FileStream outputFile = File.OpenWrite(outputFilePath))
{
    // GetInput is some method that fills inputBuffer and returns inputBuffer.Length.
    while(GetInput(ref inputBuffer) > 0)
    {
        // Compress input.
        int compressedSize = CLZF2.Compress(inputBuffer, ref outputBuffer);

        // Write compressed data to file.
        // Note the use of the size returned by Compress and not outputBuffer.Length.
        outputFile.Write(outputBuffer, 0, compressedSize);
    }
}
```

## License
This code is under a BSD license. This essentially means you can freely use it as long as you include the copyright statements as attribution. See the license file for details.
