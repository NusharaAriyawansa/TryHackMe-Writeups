# TryHackMe - Compiled Writeup

**Difficulty:** Easy  
**Category:** Reverse Engineering  
**Date:** 28-02-2026  

---

## Summary
This challenge revolves around reverse engineering a compiled binary. The goal is to analyze the binary without executing it, understand the password validation logic, and extract the correct password.

---

## Reconnaissance

The binary was found at `/root/Rooms/Compiled/Compiled.Compiled` on the AttackBox.

Checked the file type:
```bash
file Compiled.Compiled
```

Output:
```
Compiled.Compiled: ELF 64-bit LSB shared object, x86-64, not stripped
```

---

## Step 1 — Decompiling the Binary

Since the binary cannot be executed on the AttackBox, uploaded it to **Decompiler Explorer** (https://dogbolt.org) to decompile it and read the source code.

Used the **BinaryNinja** decompiler output to analyze the main function.

---

## Step 2 — Analyzing the Code

The decompiled main function revealed:

```c
int32_t main(int32_t argc, char** argv, char** envp)
{
    __builtin_strcpy(&var_48, "StringsIsForNoobs");
    fwrite("Password: ", 1, 0xa, __TMC_END__);
    char var_28[0x20];
    __isoc99_scanf("DoYouEven%sCTF", &var_28);
    int32_t rax_1 = strcmp(&var_28, "_init");
    ...
    else
        printf("Correct!");
}
```

Key observations:
- The string `"StringsIsForNoobs"` is a hint — don't use the `strings` command
- The program asks for a password
- The scanf format is `"DoYouEven%sCTF"` — it extracts the portion **between** `DoYouEven` and `CTF` into `var_28`
- The extracted value is compared against `"_init"` using strcmp

---

## Step 3 — Understanding the scanf Logic

The scanf format `"DoYouEven%sCTF"` works like this:
- It expects input in the format `DoYouEvenXXXCTF`
- It extracts only the `XXX` part into `var_28`
- Since `var_28` must equal `_init`, the input must be `DoYouEven_init`
- Note: `CTF` is NOT included at the end — scanf stops extracting before it

So the correct password is:
```
DoYouEven_init
```

---

## Flag

```
DoYouEven_init
```

---

## What is Reverse Engineering?

**Reverse Engineering** is the process of analyzing a compiled binary to understand how it works without access to the original source code. Tools like Ghidra, BinaryNinja, and Decompiler Explorer help convert machine code back into readable C-like code.

---

## Lessons Learned
- The `strings` command is not always useful — the hint `StringsIsForNoobs` confirms this
- Decompiler Explorer is a great online tool for reverse engineering binaries
- Always carefully analyze scanf format strings — they control exactly what input is captured
- `strcmp` returns 0 when strings match — trace the logic to find the winning condition