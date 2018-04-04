Tales from the kernel
=====================

Some random tales from the kernel.

April 2018 - static keys
------------------------

Reference: Documentation/static-keys.txt

Static keys allows the inclusion of seldom used features in
performance-sensitive fast-path kernel code, via a GCC feature and a code
patching technique. A quick example::

	DEFINE_STATIC_KEY_FALSE(key);

	...

        if (static_branch_unlikely(&key))
                do unlikely code
        else
                do likely code

	...
	static_branch_enable(&key);
	...
	static_branch_disable(&key);
	...

The static_branch_unlikely() branch will be generated into the code with as little
impact to the likely code path as possible.


Implementation
--------------

(paraphrased by Tobin)

static_branch_unlikely() compiles the likely code inline and adds a (5 byte)
noop instruction.  If static_branch_enable() is called the instructions are
overwritten with a jump to out of line code.