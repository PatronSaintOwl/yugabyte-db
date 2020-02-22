---
title: Concatenation
linkTitle: '|| (concatenation)'
description:  '|| (concatenation)'
menu:
  latest:
    identifier: concatenation-operator
    parent: functions-operators
    weight: 20
isTocNested: true
showAsideToc: true
---

If both sides of the operator are primitive JSON values, then the result is an _array_ of these values:

```postgresql
do $body$
declare
  j_left constant jsonb := '17';
  j_right constant jsonb := '"x"';
  j_expected constant jsonb := '[17, "x"]';
begin
  assert
    j_left || j_right = j_expected,
 'assert failed';
end;
$body$;
```

If one side is a primitive JSON value and the other is an  _array_ , then the result is an _array_:

```postgresql
do $body$
declare
  j_left constant jsonb := '17';
  j_right constant jsonb := '["x", true]';
  j_expected constant jsonb := '[17, "x", true]';
begin
  assert
    j_left || j_right = j_expected,
 'assert failed';
end;
$body$;
```

If each side is an _object_, then the results is an _object_ with all the key-value pairs present:

```postgresql
do $body$
declare
  j_left constant jsonb := '{"a": 1, "b": 2}';
  j_right constant jsonb := '{"p":17, "a": 19}';
  j_expected constant jsonb := '{"a": 19, "b": 2, "p": 17}';
begin
  assert
    j_left || j_right = j_expected,
 'assert failed';
end;
$body$;
```

If the keys of key-value pairs collide, then the last-mentioned one wins, just as when the keys of such pairs collide in a single _object_:

```postgresql
do $body$
declare
  j_left constant jsonb := '{"a": 1, "b": 2}';
  j_right constant jsonb := '{"p":17, "a": 19}';
  j_expected constant jsonb := '{"a": 19, "b": 2, "p": 17}';
begin
  assert
    j_left || j_right = j_expected,
 'assert failed';
end;
$body$;
```

If one side is an _object_ and the other is an _array_, then the _object_ is absorbed as a value in the _array_:

```postgresql
do $body$
declare
  j_left constant jsonb := '{"a": 1, "b": 2}';
  j_right constant jsonb := '[false, 42, null]';
  j_expected constant jsonb := '[{"a": 1, "b": 2}, false, 42, null]';
begin
  assert
    j_left || j_right = j_expected,
 'assert failed';
end;
$body$;
```