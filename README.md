# resample

A resample library based on [libsamplerate](https://github.com/libsndfile/libsamplerate), adapted to work with Java via JNI.

Currently, it's configured to run on Android with NDK, but you should be able to adapt it for regular Java projects easily (mainly change log code in [resample.c](resample/src/main/cpp/resample.c)).

## Project Structure & Usage

Standard gradle project, import the resample module into your own projects.

### Key files

[Resample.java](resample/src/main/java/com/jackz314/resample/Resample.java) provides Java interface for native JNI code. This should be the entry point for your code

[resample.c](resample/src/main/cpp/resample.c) contains JNI code interfacing with [libsamplerate](https://github.com/libsndfile/libsamplerate) code.

[samplerate.h](resample/src/main/cpp/samplerate.h) defines all public functions of [libsamplerate](https://github.com/libsndfile/libsamplerate).

[CMakeLists.txt](resample/src/main/cpp/CMakeLists.txt) contains directives for the JNI/NDK portion of the code.

## License

Released under [BSD-2-Clause](LICENSE).

## Documentation

Most of the documentation is in [Resample.java](resample/src/main/java/com/jackz314/resample/Resample.java) and [resample.c](resample/src/main/cpp/resample.c)

Below text is generated with [Javadoc-to-Markdown](https://github.com/delight-im/Javadoc-to-Markdown).

### `public class Resample implements Closeable`

resample audio in 16-bit PCM format using libsamplerate: https://github.com/libsndfile/libsamplerate

### `public Resample(ConverterType converterType, int channels, double convertRatio)`

Initialize a resample instance, initializes the internal SRC_STATE

 * **Parameters:**
   * `converterType` — the conversion type, see also {@link ConverterType}
   * `channels` — number of channels
   * `convertRatio` — resample conversion ratio (out to in)
 * **Exceptions:** `IllegalArgumentException` — if internal initialization fails

### `public Resample(ConverterType converterType, int channels, int inSampleRate, int outSampleRate)`

Initialize a resample instance, initializes the internal SRC_STATE

 * **Parameters:**
   * `converterType` — the conversion type, see also {@link ConverterType}
   * `channels` — number of channels
   * `inSampleRate` — input sample rate
   * `outSampleRate` — output/target sample rate

### `public byte[] process(byte[] inData)`

processes data, uses GetPrimitiveArrayCritical to avoid copy, but will disable GC shortly and could hang other threads for details see https://stackoverflow.com/questions/23258357/whats-the-trade-off-between-using-getprimitivearraycritical-and-getprimitivety or https://docs.oracle.com/javase/8/docs/technotes/guides/jni/spec/functions.html#GetPrimitiveArrayCritical_ReleasePrimitiveArrayCritical

 * **Parameters:** `inData` — input data
 * **Returns:** output data array, with the correct size

### `public byte[] processCopy(byte[] inData)`

process using the simpler copy method in JNI to get the input array (instead of GetPrimitiveArrayCritical), returns output in array

 * **Parameters:** `inData` — input data
 * **Returns:** output data array, with the correct size

### `public byte[] process(ByteBuffer inData)`

processes data, uses GetDirectBufferAddress so the buffer needs to be direct

 * **Parameters:** `inData` — input buffer, needs to be allocated using {@link ByteBuffer#allocateDirect(int)}

     set its limit with {@link ByteBuffer#limit(int)} to limit the input size
 * **Returns:** output data array, with the correct size

### `public int process(ByteBuffer inData, ByteBuffer outData)`

processes data, uses GetDirectBufferAddress so the buffers need to be direct

 * **Parameters:**
   * `inData` — input buffer, needs to be allocated using {@link ByteBuffer#allocateDirect(int)}

     set its limit with {@link ByteBuffer#limit(int)} to limit the input size
   * `outData` — output buffer, needs to be allocated using {@link ByteBuffer#allocateDirect(int)}
 * **Returns:** output size

### `public int process(ByteBuffer inData, ByteBuffer outData, int inSize)`

processes data, uses GetDirectBufferAddress so the buffers need to be direct

 * **Parameters:**
   * `inData` — input buffer, needs to be allocated using {@link ByteBuffer#allocateDirect(int)}
   * `outData` — output buffer, needs to be allocated using {@link ByteBuffer#allocateDirect(int)}
   * `inSize` — input size (read size)
 * **Returns:** output size (write size to outData)

### `public int simpleResample(ConverterType converterType, int channels, int inSampleRate, int outSampleRate, ByteBuffer inData, ByteBuffer outData, int inSize)`

performs <b>one-time</b> simple conversions (typically <b>long term</b>, otherwise the performance is bad)

 * **Parameters:**
   * `converterType` — the conversion type, see also {@link ConverterType}
   * `channels` — number of channels
   * `inSampleRate` — input sample rate
   * `outSampleRate` — output/target sample rate
   * `inData` — input buffer, needs to be allocated using {@link ByteBuffer#allocateDirect(int)}
   * `outData` — output buffer, needs to be allocated using {@link ByteBuffer#allocateDirect(int)}
 * **Returns:** output size

### `public int simpleResample(ConverterType converterType, int channels, double convertRatio, ByteBuffer inData, ByteBuffer outData, int inSize)`

performs one-time simple conversions (typically long term, otherwise the performance is bad)

 * **Parameters:**
   * `converterType` — the conversion type, see also {@link ConverterType}
   * `channels` — number of channels
   * `convertRatio` — resample conversion ration (out to in)
   * `inData` — input buffer, needs to be allocated using {@link ByteBuffer#allocateDirect(int)}
   * `outData` — output buffer, needs to be allocated using {@link ByteBuffer#allocateDirect(int)}
 * **Returns:** output size

### `public enum ConverterType`

Linear & Zero Order is really fast but has low quality, Sinc is slower but offer better quality For more details, see https://github.com/libsndfile/libsamplerate/blob/master/docs/api_misc.md#converters 