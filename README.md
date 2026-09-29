# type-compare-numbers
Comparing numbers on only type system

```typescript
type DigitArray = {
    0: []
    1: [0]
    2: [0,0]
    3: [0,0,0]
    4: [0,0,0,0]
    5: [0,0,0,0,0]
    6: [0,0,0,0,0,0]
    7: [0,0,0,0,0,0,0]
    8: [0,0,0,0,0,0,0,0]
    9: [0,0,0,0,0,0,0,0,0]
}

type Digit = keyof DigitArray

type Ascending<T extends Digit, U extends Digit> =
    DigitArray[U] extends [...DigitArray[T], ...any]?
    true: false

type NumToStr<T extends number> = `${T}`

type AscendingNumStrPadded<T extends string, U extends string> =
    T extends `${infer TD extends Digit}${infer TR}`? (
        U extends `${infer UD extends Digit}${infer UR}`? (
            Ascending<TD, UD> extends true? true:
            Ascending<UD, TD> extends true? false:
            AscendingNumStrPadded<TR, UR>
        ):
        U extends ''? false: never
    ):
    never

type StrToArr<T extends string> =
    T extends `${infer F}${infer R}`? [F, ...StrToArr<R>]: []

type LeftPadArr<T extends string[], U extends string[]> =
    T extends [...infer TF extends string[], infer TR extends string]? (
        U extends [...infer UF extends string[], infer UR extends string]?
            [`${LeftPadArr<TF, UF>[0]}${TR}`, `${LeftPadArr<TF, UF>[1]}${UR}`]:
            [`${LeftPadArr<TF, []>[0]}${TR}`, `${LeftPadArr<TF, []>[1]}0`]
    ):
    U extends [...infer UF extends string[], infer UR extends string]?
        [`${LeftPadArr<[], UF>[0]}0`, `${LeftPadArr<[], UF>[1]}${UR}`]:
        ['', '']

type PadStr<T extends string, U extends string> =
    LeftPadArr<StrToArr<T>, StrToArr<U>>

type AscendingNum<T extends number, U extends number> =
    AscendingNumStrPadded<
        PadStr<NumToStr<T>, NumToStr<U>>[0],
        PadStr<NumToStr<T>, NumToStr<U>>[1]
    >

type CompareNum<T extends number, U extends number> =
    AscendingNum<T, U> extends false? '>':
    AscendingNum<U, T> extends false? '<':
    '='
```

## Example

```typescript
type c0 = CompareNum<1555, 1> // '>'
type c1 = CompareNum<1, 1>    // '='
type c2 = CompareNum<2, 45>   // '<'
```
