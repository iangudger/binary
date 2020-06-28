# A faster reflection-based binary encoding library for Go

`encoding/binary`'s API can be a bit cumbersome and its performance leaves quite a bit to be desired. This is a re-imagining of that library.

Use of `io.Reader` and `io.Writer` seems like an unfortunate decision in `encoding/binary`. A common pattern when using the library is allocating a buffer for the encoded data and wrapping it in a `bytes.Buffer` in order to pass it to `binary.Read` or `binary.Write`. Internally, these functions allocate another buffer for the full encoding and copy between it and the `io.Reader` or `io.Writer`.

Another irritating consequence of using `io.Reader` and `io.Writer` is that it proxies your errors unnecessarily. This was actually the original motivation of this library. This library was originally written as part of the [gVisor](https://gvisor.dev/) project which uses its own error type.

In the included benchmark, this use of this library imparts roughly a 5x speedup over `encoding/binary`. This is almost entirely due to giving the library a more reasonable API, further optimization is almost certainly possible.

Please note that this library panics if the buffer provided to `Unmarshal` is not exactly the correct size. The reasoning for this is that passing an incorrect size is programmer error and not a dynamic runtime condition. `Marshal` does not have this property.
