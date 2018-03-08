#
# Kbuild for top-level directory of U-Boot
# This file takes care of the following:
# 1) Generate generic-asm-offsets.h
# 2) Generate asm-offsets.h

# Default sed regexp - multiline due to syntax constraints
define sed-y
	"s:[[:space:]]*\.ascii[[:space:]]*\"\(.*\)\":\1:; \
	/^->/{s:->#\(.*\):/* \1 */:; \
	s:^->\([^ ]*\) [\$$#]*\([-0-9]*\) \(.*\):#define \1 \2 /* \3 */:; \
	s:^->\([^ ]*\) [\$$#]*\([^ ]*\) \(.*\):#define \1 \2 /* \3 */:; \
	s:->::; p;}"
endef

# Use filechk to avoid rebuilds when a header changes, but the resulting file
# does not
define filechk_offsets
	(set -e; \
	 echo "#ifndef $2"; \
	 echo "#define $2"; \
	 echo "/*"; \
	 echo " * DO NOT MODIFY."; \
	 echo " *"; \
	 echo " * This file was generated by Kbuild"; \
	 echo " */"; \
	 echo ""; \
	 sed -ne $(sed-y); \
	 echo ""; \
	 echo "#endif" )
endef

#####
# 1) Generate generic-asm-offsets.h

generic-offsets-file := include/generated/generic-asm-offsets.h

always  := $(generic-offsets-file)
targets := $(generic-offsets-file) lib/asm-offsets.s

# We use internal kbuild rules to avoid the "is up to date" message from make
lib/asm-offsets.s: lib/asm-offsets.c FORCE
	$(Q)mkdir -p $(dir $@)
	$(call if_changed_dep,cc_s_c)

$(obj)/$(generic-offsets-file): lib/asm-offsets.s FORCE
	$(call filechk,offsets,__GENERIC_ASM_OFFSETS_H__)

#####
# 2) Generate asm-offsets.h
#

ifneq ($(wildcard $(srctree)/arch/$(ARCH)/lib/asm-offsets.c),)
offsets-file := include/generated/asm-offsets.h
endif

always  += $(offsets-file)
targets += $(offsets-file)
targets += arch/$(ARCH)/lib/asm-offsets.s

CFLAGS_asm-offsets.o := -DDO_DEPS_ONLY

# We use internal kbuild rules to avoid the "is up to date" message from make
arch/$(ARCH)/lib/asm-offsets.s: arch/$(ARCH)/lib/asm-offsets.c FORCE
	$(Q)mkdir -p $(dir $@)
	$(call if_changed_dep,cc_s_c)

$(obj)/$(offsets-file): arch/$(ARCH)/lib/asm-offsets.s FORCE
	$(call filechk,offsets,__ASM_OFFSETS_H__)
