load("//build/bazel_common_rules/dist:dist.bzl", "copy_to_dist_dir")

# TODO: import clang as a toolchain
clang = "prebuilts/clang/host/linux-x86/clang-r522817"

make = "./prebuilts/build-tools/linux-x86/bin/make"

clang_bin = "../{toolchain}/bin/".format(toolchain = clang)

filegroup(
    name = "lk_srcs",
    srcs = glob(["**"]),
)

filegroup(
    name = "lk_toochain",
    srcs = [
        "//prebuilts/build-tools:linux-x86",
        "//{toolchain}:binaries".format(toolchain = clang),
    ],
)

environment_variables = {
    "CC": clang_bin + "clang -target {clang_target}",
    "LD": clang_bin + "ld.lld",
    "OBJDUMP": clang_bin + "llvm-objdump",
    "OBJCOPY": clang_bin + "llvm-objcopy",
    "CPPFILT": clang_bin + "llvm-cxxfilt",
    "SIZE": clang_bin + "llvm-size",
    "NM": clang_bin + "llvm-nm",
    "STRIP": clang_bin + "llvm-strip",
    "LIBGCC": "",
}

environment = " ".join(['{}="{}"'.format(k, v) for k, v in environment_variables.items()])

build_command = "{make} -C lk {environment} {lk_target} && cp -R lk/build-{lk_target}/lk.elf $@"

genrule(
    name = "qemu_riscv64",
    srcs = [
        ":lk_srcs",
        ":lk_toochain",
    ],
    outs = [
        "qemu_riscv64/lk.elf",
    ],
    cmd = build_command.format(
        environment = environment.format(clang_target = "riscv64-unknown-elf"),
        lk_target = "qemu-virt-riscv64-test",
        make = make,
    ),
    tools = [
        ":lk_toochain",
    ],
)

copy_to_dist_dir(
    name = "qemu_riscv64_dist",
    data = [":qemu_riscv64"],
    dist_dir = "out/lk/dist",
    flat = True,
)

genrule(
    name = "qemu_arm64",
    srcs = [
        ":lk_srcs",
        ":lk_toochain",
    ],
    outs = [
        "qemu_arm64/lk.elf",
    ],
    cmd = build_command.format(
        environment = environment.format(clang_target = "aarch64-unknown-elf"),
        lk_target = "qemu-virt-arm64-test",
        make = make,
    ),
    tools = [
        ":lk_toochain",
    ],
)

copy_to_dist_dir(
    name = "qemu_arm64_dist",
    data = [":qemu_arm64"],
    dist_dir = "out/lk/dist",
    flat = True,
)
