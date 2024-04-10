# Mutations

Here you could find some mutations made manually just to be able to explain how it should be done,
I discourage you to do mutation testing this way

Original code
```kotlin
fun execute(first: Int, last: Int) {
    val values = translator.execute(first, last)
    var position = 0
    while (position < values.size) {
        printer.execute(values[position])
        position++
    }
}
```

## Changed conditional boundary
### Mutant
```kotlin
fun execute(first: Int, last: Int) {
    val values = translator.execute(first, last)
    var position = 0
    while (position <= values.size) {
        printer.execute(values[position])
        position++
    }
}
```
### Way to kill the mutant
```kotlin
@Test
fun `should print all the number between 1 and 100`() {
    val range = 1..100
    every { counter.count(any(), any()) } returns range

    main.execute(range.first, range.last)
}
```
## Negate conditional

### Mutant
```kotlin
fun execute(first: Int, last: Int) {
    val values = translator.execute(first, last)
    var position = 0
    while (position >= values.size) {
        printer.execute(values[position])
        position++
    }
}
```
### Way to kill the mutant
```kotlin
@Test
fun `should print all the number between 1 and 100`() {
    val range = 1..100
    every { counter.count(any(), any()) } returns range

    main.execute(range.first, range.last)

    verify(exactly = range.count()) { printer.execute(any()) }
}
```

## Remove call
### Mutant
```kotlin
fun execute(first: Int, last: Int) {
    val values = translator.execute(first, last)
    var position = 0
    while (position < values.size) {
        position++
    }
}
```
### Way to kill the mutant
```kotlin
@Test
fun `should print all the number between 1 and 100`() {
    val range = 1..100
    every { counter.count(any(), any()) } returns range

    main.execute(range.first, range.last)

    verify(exactly = range.count()) { printer.execute(any()) }
}
```

Original code
```kotlin
 fun execute(start: Int, end: Int): List<String> {
    val list = mutableListOf<String>()
    counter.count(start, end).map {
        when {
            it.isMultipleOf(15) -> list.add(multipleOf15Translation())
            it.isMultipleOf(3) -> list.add(multipleOf3Translation())
            it.isMultipleOf(5) -> list.add(multipleOf5Translation())
            else -> list.add("$it")
        }
    }
    return list
}
```

## Replaced return value with Collections.emptyList
### Mutant
```kotlin
 fun execute(start: Int, end: Int): List<String> {
    val list = mutableListOf<String>()
    counter.count(start, end).map {
        when {
            it.isMultipleOf(15) -> list.add(multipleOf15Translation())
            it.isMultipleOf(3) -> list.add(multipleOf3Translation())
            it.isMultipleOf(5) -> list.add(multipleOf5Translation())
            else -> list.add("$it")
        }
    }
    return listOf()
}
```
### Way to kill the mutant (easiest version)
```kotlin
@Test
fun `should return Fizz when number is multiple of 3`() {
    every { counter.count(any(), any()) } returns IntRange(3, 3)

    val response = fizzBuzzTranslator.execute(3, 3)

    assertEquals(listOf("Fizz"), response)
}

```

## Replaced return value with ""
### Mutant
```kotlin
 private fun multipleOf5Translation() = ""
```
### Way to kill the mutant 
```kotlin
 @Test
fun `should return Buzz when number is multiple of 5`() {
    every { counter.count(any(), any()) } returns IntRange(5, 5)

    val response = fizzBuzzTranslator.execute(5, 5)

    assertEquals(listOf("Buzz"), response)
}
```

## Replaced integer modulus with multiplication
### Mutant
```kotlin
 private fun Int.isMultipleOf(multiple: Int): Boolean {
    return this * multiple == 0
}
```
### Way to kill the mutant 
This mutant can be kill with any of the two previous tests and with this one
```kotlin
@Test
fun `should return FizzBuzz when number is multiple of 3 and 5`() {
    every { counter.count(any(), any()) } returns IntRange(15, 15)

    val response = fizzBuzzTranslator.execute(15, 15)

    assertEquals(listOf("FizzBuzz"), response)
}
```
Original code
```
private fun Int.isMultipleOf(multiple: Int): Boolean {
    return this % multiple == 0
}

```

## Replaced boolean return with true
### Mutant
```kotlin
 private fun Int.isMultipleOf(multiple: Int): Boolean {
    return true
}
```
### Way to kill the mutant 
This mutant can be kill with any of the three previous tests and with this one
```kotlin
@Test
fun `should return Fizz, Buzz,FizzBuzz or number with a complete range`() {
    every { counter.count(any(), any()) } returns IntRange(1, 100)

    val response = fizzBuzzTranslator.execute(1, 100)

    assertEquals(listOf("1", "2", "Fizz", "4", "Buzz", "Fizz", "7", "8", "Fizz", "Buzz", "11", "Fizz", "13", "14", "FizzBuzz", "16", "17",
        "Fizz", "19", "Buzz", "Fizz", "22", "23", "Fizz", "Buzz", "26", "Fizz", "28", "29", "FizzBuzz", "31", "32",
        "Fizz", "34", "Buzz", "Fizz", "37", "38", "Fizz", "Buzz", "41", "Fizz", "43", "44", "FizzBuzz", "46", "47",
        "Fizz", "49", "Buzz", "Fizz", "52", "53", "Fizz", "Buzz", "56", "Fizz", "58", "59", "FizzBuzz", "61", "62",
        "Fizz", "64", "Buzz", "Fizz", "67", "68", "Fizz", "Buzz", "71", "Fizz", "73", "74", "FizzBuzz", "76", "77",
        "Fizz", "79", "Buzz", "Fizz", "82", "83", "Fizz", "Buzz", "86", "Fizz", "88", "89", "FizzBuzz", "91", "92",
        "Fizz", "94", "Buzz", "Fizz", "97", "98", "Fizz", "Buzz"), response)
}
```
