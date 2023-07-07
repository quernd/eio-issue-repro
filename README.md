This is essentially the README example of https://github.com/anmonteiro/piaf with `Eio.Fiber.any []` added so the program will keep running.

Run with
`EIO_BACKEND=posix OCAMLRUNPARAM=v=4095 dune exec --profile=release ./test.exe`
and see the GC hard at work.

If you run it with `strace`, you'll see:

```
write(5, "\27\3\3\0\"vl.9)\310\313\3369V\212\246^v\332\1\342m\372\267t\310W\327\360(#"..., 39) = 39
write(5, "\27\3\3\0\23\0004\354~CG\361\255 \252U\304\23\7\270\25f\10,", 24) = 24
close(5)                                = 0
ppoll([{fd=-1}, {fd=-1}, {fd=-1}, {fd=3, events=POLLIN}, {fd=-1}, {fd=5, events=POLLIN}], 6, NULL, NULL, 8) = 1 ([{fd=5, revents=POLLNVAL}])
ppoll([{fd=-1}, {fd=-1}, {fd=-1}, {fd=3, events=POLLIN}, {fd=-1}, {fd=5, events=POLLIN}], 6, NULL, NULL, 8) = 1 ([{fd=5, revents=POLLNVAL}])
```

and it keeps polling in an infinite loop because it ignores POLLNVAL (the file descriptor is invalid because it has just been closed).
