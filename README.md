# testutil [![GoDoc](https://godoc.org/github.com/joshlf/testutil?status.svg)](https://godoc.org/github.com/joshlf/testutil)

Package `testutil` provides utilities to make common testing tasks easier.

## Checking errors

#### Nil errors

A common pattern in testing is to check that an error is nil, and fail if it is
non-nil. For example:

```
func TestFoo(t *testing.T) {
  f, err := getFoo()
  if err != nil {
    t.Fatalf("could not get foo:", err)
  }
  ...
}
```

`testutil` provides a number of convenience functions to make this common task easier such as `Must`, which checks that its argument is nil, and otherwise calls `t.Fatal`:

```
func TestFoo(t *testing.T) {
  f, err := getFoo()
  testutil.Must(t, err)
  ...
}
```

#### Non-nil errors

Conversely, it's also common to check that a particular error is returned:

```
func TestFooError(t *testing.T) {
  _, err := getFoo()
  if err == nil || err.Error() != "error string" {
    t.Errorf("unexpected error: got %v; want %v", err, "error string")
  }
}
```

`testutil` makes this easy as well:

```
func TestFooError(t *testing.T) {
  _, err := getFoo()
  testutil.MustError(t, "error string", err)
}
```

## Creating temporary files

Tests often rely on creating temporary files or directories, which results in a lot of boilerplate:

```
func TestTempFile(t *testing.T) {
  f, err := ioutil.TempFile("", "")
  if err != nil {
    t.Fatal("could not create temp file:", err)
  }
  err = os.Remove(f.Name())
  if err != nil {
    t.Fatalf("could not remove temp file:", err)
  }
}
```

`testutil` handles this simply as well:

```
func TestTempFile(t *testing.T) {
  f := testutil.MustTempFile(t, "", "")
  testutil.Must(os.Remove(f.Name()))
}
```
