# FIXME: add company header

# TODO: add our BTF archive as a git submodule here and install BTF files for different OS/platform combinations
#  to different components

# FIXME: this is temporary until we have our own BTF archive ironed out
install(
        DIRECTORY .
        DESTINATION "${BTF_INSTALL_PATH}"
        COMPONENT BTF
        FILES_MATCHING PATTERN "*.btf.xz"
)
