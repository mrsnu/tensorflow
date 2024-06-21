# Description:
# TensorFlow is a computational framework, primarily for use in machine
# learning applications.

load("@bazel_skylib//lib:selects.bzl", "selects")
load("@bazel_skylib//rules:common_settings.bzl", "bool_flag", "bool_setting")
load(
    "//tensorflow:tensorflow.bzl",
    "VERSION",
    "VERSION_MAJOR",
    "if_google",
    "if_oss",
    "tf_cc_shared_object",
    "tf_custom_op_library_additional_deps_impl",
    "tf_native_cc_binary",
)
load(
    "//tensorflow/core/platform:build_config.bzl",
    "tf_additional_binary_deps",
)
load(
    "//third_party/mkl:build_defs.bzl",
    "if_mkl_ml",
)
load("@bazel_skylib//:bzl_library.bzl", "bzl_library")
load("//tensorflow:tensorflow.bzl", "tf_cc_shared_library")

# copybara:uncomment_begin
# load("//tools/build_defs/license:license.bzl", "license")
# copybara:uncomment_end

# copybara:comment_begin(oss-only)
load(
    "//tensorflow/python/tools/api/generator:api_gen.bzl",
    "gen_api_init_files",
    "get_compat_files",
    "get_nested_compat_files",
)
load(
    "//tensorflow/python/tools/api/generator:api_init_files.bzl",
    "TENSORFLOW_API_INIT_FILES",
)
load(
    "//tensorflow/python/tools/api/generator:api_init_files_v1.bzl",
    "TENSORFLOW_API_INIT_FILES_V1",
)
# copybara:comment_end

PACKAGE_STATIC_DEPS = [
    "//:__subpackages__",
    "@cudnn_frontend_archive//:__subpackages__",
    "@gif//:__subpackages__",
    "@highwayhash//:__subpackages__",
    "@hwloc//:__subpackages__",
    "@icu//:__subpackages__",
    "@libjpeg_turbo//:__subpackages__",
    "@llvm-project//:__subpackages__",
    "@nsync//:__subpackages__",
    "@png//:__subpackages__",
    "@sobol_data//:__subpackages__",
    "@bazel_tools//:__subpackages__",
    "@boringssl//:__subpackages__",
    "@com_github_cares_cares//:__subpackages__",
    "@com_github_googlecloudplatform_tensorflow_gcp_tools//:__subpackages__",
    "@com_github_grpc_grpc//:__subpackages__",
    "@com_google_absl//:__subpackages__",
    "@com_google_googleapis//:__subpackages__",
    "@com_google_protobuf//:__subpackages__",
    "@com_googlesource_code_re2//:__subpackages__",
    "@compute_library//:__subpackages__",
    "@curl//:__subpackages__",
    "@double_conversion//:__subpackages__",
    "@eigen_archive//:__subpackages__",
    "@farmhash_archive//:__subpackages__",
    "@farmhash_gpu_archive//:__subpackages__",
    "@fft2d//:__subpackages__",
    "@gemmlowp//:__subpackages__",
    "@jsoncpp_git//:__subpackages__",
    "@libxsmm_archive//:__subpackages__",
    "@llvm_openmp//:__subpackages__",
    "@llvm_terminfo//:__subpackages__",
    "@llvm_zlib//:__subpackages__",
    "@lmdb//:__subpackages__",
    "@local_config_cuda//:__subpackages__",
    "@local_config_git//:__subpackages__",
    "@local_config_nccl//:__subpackages__",
    "@local_config_rocm//:__subpackages__",
    "@local_config_tensorrt//:__subpackages__",
    "@local_execution_config_platform//:__subpackages__",
    "@mkl_dnn_acl_compatible//:__subpackages__",
    "@mkl_dnn_v1//:__subpackages__",
    "@org_sqlite//:__subpackages__",
    "@platforms//:__subpackages__",
    "@snappy//:__subpackages__",
    "@upb//:__subpackages__",
    "@zlib//:__subpackages__",
]

package(
    # copybara:uncomment default_applicable_licenses = [":license"],
    default_visibility = [":internal"],
)

# copybara:uncomment_begin
# license(
#     name = "license",
#     package_name = "tensorflow",
# )
# copybara:uncomment_end

licenses(["notice"])

exports_files([
    "LICENSE",
    # The leakr files are used by //third_party/cloud_tpu and
    # //third_party/tensorboard/google:copybara_config_test.
    "leakr_badwords.dic",
    "leakr_badfiles.dic",
    "leakr_file_type_recipe.ftrcp",
])

# copybara:comment_begin(oss-only)
TENSORFLOW_API_INIT_FILES_V2 = (
    TENSORFLOW_API_INIT_FILES +
    get_compat_files(TENSORFLOW_API_INIT_FILES, 2) +
    get_compat_files(TENSORFLOW_API_INIT_FILES_V1, 1) + get_nested_compat_files([
        1,
        2,
    ])
)

TENSORFLOW_API_INIT_FILES_V1 = (
    TENSORFLOW_API_INIT_FILES_V1 +
    get_compat_files(TENSORFLOW_API_INIT_FILES, 2) +
    get_compat_files(TENSORFLOW_API_INIT_FILES_V1, 1) + get_nested_compat_files([
        1,
        2,
    ])
)
# copybara:comment_end

# Config setting used when building for products
# which requires restricted licenses to be avoided.
config_setting(
    name = "no_lgpl_deps",
    define_values = {"__TENSORFLOW_NO_LGPL_DEPS__": "1"},
    visibility = ["//visibility:public"],
)

# Config setting that disables the default logger, only logging
# to registered TFLogSinks
config_setting(
    name = "no_default_logger",
    define_values = {"no_default_logger": "true"},
    visibility = ["//visibility:public"],
)

# Config setting for determining if we are building for Android.
config_setting(
    name = "android",
    flag_values = if_google(
        {"//tools/cpp:cc_target_os": "android"},
        {},
    ),
    values = if_oss(
        {"crosstool_top": "//external:android/crosstool"},
        {},
    ),
    visibility = ["//visibility:public"],
)

config_setting(
    name = "android_x86",
    flag_values = if_google(
        {"//tools/cpp:cc_target_os": "android"},
        {},
    ),
    values = dict(
        if_oss(
            {"crosstool_top": "//external:android/crosstool"},
        ),
        cpu = "x86",
    ),
    visibility = ["//visibility:public"],
)

config_setting(
    name = "android_x86_64",
    flag_values = if_google(
        {"//tools/cpp:cc_target_os": "android"},
        {},
    ),
    values = dict(
        if_oss(
            {"crosstool_top": "//external:android/crosstool"},
        ),
        cpu = "x86_64",
    ),
    visibility = ["//visibility:public"],
)

config_setting(
    name = "android_armeabi",
    flag_values = if_google(
        {"//tools/cpp:cc_target_os": "android"},
        {},
    ),
    values = dict(
        if_oss(
            {"crosstool_top": "//external:android/crosstool"},
        ),
        cpu = "armeabi",
    ),
    visibility = ["//visibility:public"],
)

# copybara:uncomment_begin(google-only)
# config_setting(
#     name = "chromiumos_x86_64",
#     flag_values = {"//tools/cpp:cc_target_os": "chromiumos"},
#     values = {"cpu": "k8"},
#     visibility = ["//visibility:public"],
# )
#
# config_setting(
#     name = "chromiumos_arm64",
#     flag_values = {"//tools/cpp:cc_target_os": "chromiumos"},
#     values = {"cpu": "arm"},
#     visibility = ["//visibility:public"],
# )
#
# config_setting(
#     name = "chromiumos_armv7",
#     flag_values = {"//tools/cpp:cc_target_os": "chromiumos"},
#     values = {"cpu": "armeabi-v7a"},
#     visibility = ["//visibility:public"],
# )
# copybara:uncomment_end

config_setting(
    name = "emscripten",
    flag_values = if_google(
        {"//tools/cpp:cc_target_os": "emscripten"},
        {},
    ),
    values = if_oss(
        {"crosstool_top": "//external:android/emscripten"},
        {},
    ),
    visibility = ["//visibility:public"],
)

config_setting(
    name = "raspberry_pi_armeabi",
    values = {
        "crosstool_top": "@local_config_arm_compiler//:toolchain",
        "cpu": "armeabi",
    },
    visibility = ["//visibility:public"],
)

config_setting(
    name = "android_arm",
    flag_values = if_google(
        {"//tools/cpp:cc_target_os": "android"},
        {},
    ),
    values = dict(
        if_oss(
            {"crosstool_top": "//external:android/crosstool"},
        ),
        cpu = "armeabi-v7a",
    ),
    visibility = ["//visibility:public"],
)

config_setting(
    name = "android_arm64",
    flag_values = if_google(
        {"//tools/cpp:cc_target_os": "android"},
        {},
    ),
    values = dict(
        if_oss(
            {"crosstool_top": "//external:android/crosstool"},
        ),
        cpu = "arm64-v8a",
    ),
    visibility = ["//visibility:public"],
)

config_setting(
    name = "android_mips",
    values = {
        "crosstool_top": "//external:android/crosstool",
        "cpu": "mips",
    },
    visibility = ["//visibility:public"],
)

config_setting(
    name = "android_mips64",
    values = {
        "crosstool_top": "//external:android/crosstool",
        "cpu": "mips64",
    },
    visibility = ["//visibility:public"],
)

config_setting(
    name = "windows",
    # Internal builds query the target OS.
    flag_values = if_google(
        {"//tools/cpp:cc_target_os": "windows"},
        {},
    ),
    # OSS builds query the CPU type.
    values = if_oss(
        {"cpu": "x64_windows"},
        {},
    ),
    visibility = ["//visibility:public"],
)

config_setting(
    name = "no_tensorflow_py_deps",
    define_values = {"no_tensorflow_py_deps": "true"},
    visibility = ["//visibility:public"],
)

config_setting(
    name = "macos_x86_64",
    flag_values = if_google(
        {"//tools/cpp:cc_target_os": "apple"},
        {},
    ),
    values = {
        "apple_platform_type": "macos",
        "cpu": if_google("darwin_x86_64", "darwin"),
    },
    visibility = ["//visibility:public"],
)

config_setting(
    name = "macos_arm64",
    flag_values = if_google(
        {"//tools/cpp:cc_target_os": "apple"},
        {},
    ),
    values = {
        "apple_platform_type": "macos",
        "cpu": "darwin_arm64",
    },
    visibility = ["//visibility:public"],
)

selects.config_setting_group(
    name = "macos",
    match_any = [
        ":macos_x86_64",
        ":macos_arm64",
    ],
    visibility = ["//visibility:public"],
)

config_setting(
    name = "ios",
    flag_values = if_google(
        {"//tools/cpp:cc_target_os": "apple"},
        {},
    ),
    values = if_oss(
        {"apple_platform_type": "ios"},
        {},
    ),
    visibility = ["//visibility:public"],
)

config_setting(
    name = "fuchsia",
    flag_values = if_google(
        {"//tools/cpp:cc_target_os": "fuchsia"},
        {},
    ),
    values = if_oss(
        # TODO(b/149248802) When we have a Fuchsia Bazel SDK update to use the values it sets.
        {"cpu": "fuchsia"},
        {},
    ),
    visibility = ["//visibility:public"],
)

config_setting(
    name = "fuchsia_x86_64",
    flag_values = if_google(
        {"//tools/cpp:cc_target_os": "fuchsia"},
        {},
    ),
    values = {
        "cpu": "x86_64",
    },
    visibility = ["//visibility:public"],
)

config_setting(
    name = "ios_x86_64",
    flag_values = if_google(
        {"//tools/cpp:cc_target_os": "apple"},
        {},
    ),
    values = dict(
        if_oss(
            {"crosstool_top": "//tools/osx/crosstool:crosstool"},
        ),
        cpu = "ios_x86_64",
    ),
    visibility = ["//visibility:public"],
)

config_setting(
    name = "chromiumos",
    flag_values = if_google(
        {"//tools/cpp:cc_target_os": "chromiumos"},
        {},
    ),
    values = if_oss(
        {"crosstool_top": "//external:android/chromiumos"},
        {},
    ),
    visibility = ["//visibility:public"],
)

config_setting(
    name = "linux_aarch64",
    values = {"cpu": "aarch64"},
    visibility = ["//visibility:public"],
)

config_setting(
    name = "linux_armhf",
    values = {"cpu": "armhf"},
    visibility = ["//visibility:public"],
)

config_setting(
    name = "linux_x86_64",
    values = {"cpu": "k8"},
    visibility = ["//visibility:public"],
)

# This condition takes precedence over :linux_x86_64
config_setting(
    name = "linux_x86_64_no_sse",
    values = {
        "cpu": "k8",
        "copt": "-mno-sse4.2",
    },
    visibility = ["//visibility:public"],
)

config_setting(
    name = "linux_ppc64le",
    values = {"cpu": "ppc"},
    visibility = ["//visibility:public"],
)

config_setting(
    name = "linux_s390x",
    values = {"cpu": "s390x"},
    visibility = ["//visibility:public"],
)

config_setting(
    name = "linux_mips64",
    values = {"cpu": "mips64"},
    visibility = ["//visibility:public"],
)

config_setting(
    name = "linux_riscv64",
    values = {"cpu": "riscv64"},
    visibility = ["//visibility:public"],
)

config_setting(
    name = "debug",
    values = {
        "compilation_mode": "dbg",
    },
    visibility = ["//visibility:public"],
)

config_setting(
    name = "optimized",
    values = {
        "compilation_mode": "opt",
    },
    visibility = ["//visibility:public"],
)

config_setting(
    name = "arm",
    values = {"cpu": "arm"},
    visibility = ["//visibility:public"],
)

config_setting(
    name = "armeabi",
    values = {"cpu": "armeabi"},
    visibility = ["//visibility:public"],
)

config_setting(
    name = "armeabi-v7a",
    values = {"cpu": "armeabi-v7a"},
    visibility = ["//visibility:public"],
)

config_setting(
    name = "arm64-v8a",
    values = {"cpu": "arm64-v8a"},
    visibility = ["//visibility:public"],
)

selects.config_setting_group(
    name = "arm_any",
    match_any = [
        ":arm",
        ":armeabi",
        ":armeabi-v7a",
        ":arm64-v8a",
        ":linux_aarch64",
        ":linux_armhf",
    ],
)

config_setting(
    name = "freebsd",
    values = {"cpu": "freebsd"},
    visibility = ["//visibility:public"],
)

# Features that are default ON are handled differently below.
#
config_setting(
    name = "no_gcp_support",
    define_values = {"no_gcp_support": "true"},
    visibility = ["//visibility:public"],
)

config_setting(
    name = "no_nccl_support",
    define_values = dict(
        if_google({"GOOGLE_CUDA_COMPILER": "clang"}),
        no_nccl_support = "true",
    ),
    visibility = ["//visibility:public"],
)

# Experimental features
config_setting(
    name = "stackdriver_support",
    define_values = {"stackdriver_support": "true"},
    visibility = ["//visibility:public"],
)

config_setting(
    name = "with_xla_support",
    define_values = {"with_xla_support": "true"},
    visibility = ["//visibility:public"],
)

# By default, XLA GPU is compiled into tensorflow when building with
# --config=cuda even when `with_xla_support` is false. The config setting
# here allows us to override the behavior if needed.
config_setting(
    name = "no_xla_deps_in_cuda",
    define_values = {"no_xla_deps_in_cuda": "true"},
    visibility = ["//visibility:public"],
)

config_setting(
    name = "with_numa_support",
    define_values = {"with_numa_support": "true"},
    visibility = ["//visibility:public"],
)

# Crosses between framework_shared_object and a bunch of other configurations
# due to limitations in nested select() statements.
config_setting(
    name = "framework_shared_object",
    define_values = {"framework_shared_object": "true"},
    visibility = ["//visibility:public"],
)

config_setting(
    name = "macos_x86_64_with_framework_shared_object",
    define_values = {
        "framework_shared_object": "true",
    },
    values = {
        "apple_platform_type": "macos",
        "cpu": "darwin",
    },
    visibility = ["//visibility:public"],
)

config_setting(
    name = "macos_arm64_with_framework_shared_object",
    define_values = {
        "framework_shared_object": "true",
    },
    values = {
        "apple_platform_type": "macos",
        "cpu": "darwin_arm64",
    },
    visibility = ["//visibility:public"],
)

selects.config_setting_group(
    name = "macos_with_framework_shared_object",
    match_any = [
        ":macos_x86_64_with_framework_shared_object",
        ":macos_arm64_with_framework_shared_object",
    ],
)

# Config setting that is satisfied when TensorFlow is being built with CUDA
# support through e.g. `--config=cuda` (or `--config=cuda_clang` in OSS).
alias(
    name = "is_cuda_enabled",
    actual = if_oss(
        "@local_config_cuda//:is_cuda_enabled",
        "@local_config_cuda//cuda:using_clang",
    ),
)

# Config setting that is satisfied when CUDA device code should be compiled
# with clang. It does not imply that CUDA support has been enabled.
alias(
    name = "is_cuda_compiler_clang",
    actual = if_oss(
        "@local_config_cuda//:is_cuda_compiler_clang",
        "@local_config_cuda//cuda:TRUE",
    ),
)

# Config setting that is satisfied when CUDA device code should be compiled
# with nvcc. It does not imply that CUDA support has been enabled.
alias(
    name = "is_cuda_compiler_nvcc",
    actual = if_oss(
        "@local_config_cuda//:is_cuda_compiler_nvcc",
        "@local_config_cuda//cuda:FALSE",
    ),
)

# Config setting that is satisfied when building with --config=cuda in OSS.
selects.config_setting_group(
    name = "is_cuda_enabled_and_oss",
    match_all = [
        ":is_cuda_enabled",
        ":oss",
    ],
)

# Config setting that is satisfied when building with --config=cuda for Windows
selects.config_setting_group(
    name = "is_cuda_enabled_and_windows",
    match_all = [
        ":is_cuda_enabled",
        ":windows",
    ],
)

# Config setting to use in select()s to distinguish open source build from
# google internal build on configurable attributes.
#
# For non-configurable distinction between OSS and Google builds, see
# `if_oss()` and `if_google()` macros in tensorflow.bzl.
config_setting(
    name = "oss",
    flag_values = {":oss_setting": "True"},
    visibility = ["//visibility:public"],
)

# Non-configurable setting to indicate open source build.
bool_setting(
    name = "oss_setting",
    build_setting_default = if_oss(True, False),
    visibility = ["//visibility:private"],
)

# Setting to use when loading kernels dynamically
config_setting(
    name = "dynamic_loaded_kernels",
    define_values = {
        "dynamic_loaded_kernels": "true",
        "framework_shared_object": "true",
    },
    visibility = ["//visibility:public"],
)

config_setting(
    name = "using_rocm_hipcc",
    define_values = {"using_rocm_hipcc": "true"},
)

config_setting(
    name = "override_eigen_strong_inline",
    define_values = {"override_eigen_strong_inline": "true"},
    visibility = ["//visibility:public"],
)

# This flag specifies whether TensorFlow 2.0 API should be built instead
# of 1.* API. Note that TensorFlow 2.0 API is currently under development.
config_setting(
    name = "api_version_2",
    define_values = {"tf_api_version": "2"},
    visibility = ["//visibility:public"],
)

# This flag enables experimental MLIR support.
config_setting(
    name = "with_mlir_support",
    define_values = {"with_mlir_support": "true"},
    visibility = ["//visibility:public"],
)

# This flag forcibly enables experimental MLIR bridge support.
config_setting(
    name = "enable_mlir_bridge",
    define_values = {"enable_mlir_bridge": "true"},
    visibility = ["//visibility:public"],
)

# This flag forcibly disables experimental MLIR bridge support.
config_setting(
    name = "disable_mlir_bridge",
    define_values = {"enable_mlir_bridge": "false"},
    visibility = ["//visibility:public"],
)

# This flag enables experimental TPU support
bool_flag(
    name = "enable_tpu_support",
    build_setting_default = False,
)

config_setting(
    name = "with_tpu_support_define",
    define_values = {"with_tpu_support": "true"},
    visibility = ["//visibility:public"],
)

config_setting(
    name = "with_tpu_support_flag",
    flag_values = {":enable_tpu_support": "True"},
    visibility = ["//visibility:public"],
)

selects.config_setting_group(
    name = "with_tpu_support",
    match_any = [
        ":with_tpu_support_define",
        ":with_tpu_support_flag",
    ],
    visibility = ["//visibility:public"],
)

# Specifies via a config setting if this is a mobile build or not, makes
# it easier to combine settings later.
selects.config_setting_group(
    name = "mobile",
    match_any = [
        ":android",
        ":chromiumos",
        ":fuchsia",
        ":emscripten",
        ":ios",
    ],
    visibility = if_google(["//visibility:public"]),
)

# This flag disables all google production dependencies, intended for
# applications run with non-prod environment.
# TODO(timshen): Currently this option only disables some dependencies.
# See b/122528503.
# copybara:uncomment_begin(google-only)
# bool_flag(
#     name = "tf_no_prod_deps",
#     build_setting_default = False,
# )
#
# config_setting(
#     name = "no_prod_deps_define",
#     define_values = {"tf_no_prod_deps": "1"},
# )
#
# config_setting(
#     name = "no_prod_deps_flag",
#     flag_values = {":tf_no_prod_deps": "True"},
# )
#
# selects.config_setting_group(
#     name = "no_prod_deps",
#     match_any = [
#         ":no_prod_deps_define",
#         ":no_prod_deps_flag",
#     ],
# )
#
# config_setting(
#     name = "no_prod_deps_cuda",
#     define_values = {
#         "tf_no_prod_deps": "1",
#         "GOOGLE_CUDA_COMPILER": "clang",
#     },
# )
# copybara:uncomment_end

config_setting(
    name = "lite_protos_legacy",
    define_values = {"TENSORFLOW_PROTOS": "lite"},
    visibility = ["//visibility:private"],
)

config_setting(
    name = "full_protos",
    define_values = {"TENSORFLOW_PROTOS": "full"},
    visibility = ["//visibility:public"],
)

selects.config_setting_group(
    name = "lite_protos",
    match_any = [":lite_protos_legacy"],
    visibility = if_google(["//visibility:public"]),
)

selects.config_setting_group(
    name = "mobile_lite_protos",
    match_all = [
        ":lite_protos",
        ":mobile",
    ],
    visibility = if_google(["//visibility:public"]),
)

selects.config_setting_group(
    name = "mobile_full_protos",
    match_all = [
        ":full_protos",
        ":mobile",
    ],
    visibility = if_google(["//visibility:public"]),
)

# copybara:uncomment_begin(google-only)
# config_setting(
#     name = "portable_proto_force_third_party",
#     define_values = {"PORTABLE_PROTO_TRANSITION_MODE": "third_party"},
#     visibility = ["//visibility:public"],
# )
# copybara:uncomment_end

# 'enable_registration_v2' opts-in to a different implementation of op and
# kernel registration - REGISTER_OP, REGISTER_KERNEL_BUILDER, etc.
#
# This setting is currently experimental. The 'v2' implementation does _not_
# correspond to a particular, finalized design; rather, it relates to
# developing one.
#
# The current aim of the 'v2' implementation is to allow 'unused' ops and
# kernels to be discarded by the linker (to the benefit of binary size).
bool_flag(
    name = "enable_registration_v2",
    build_setting_default = False,
    visibility = ["//visibility:public"],
)

config_setting(
    name = "registration_v1",
    flag_values = {":enable_registration_v2": "False"},
    visibility = ["//visibility:public"],
)

config_setting(
    name = "registration_v2",
    flag_values = {":enable_registration_v2": "True"},
    visibility = ["//visibility:public"],
)

# DO NOT ADD ANY NEW EXCEPTIONS TO THIS LIST!
# Instead, please use public APIs or public build rules TF provides.
# If you need functionality that is not exposed, we will work with you to expand our public APIs.
# TODO(b/173549186): Move Google-internal TF code out of learning/brain
package_group(
    name = "internal",
    packages = [
        "//devtools/python/indexer/...",
        "//learning/brain/keras/...",
        "//learning/brain/mlir/...",
        "//learning/brain/tfrt/...",
        "//learning/lib/ami/simple_ml/...",
        "//tensorflow/...",
        "//tensorflow_decision_forests/...",
        "//tensorflow_federated/...",
        "//third_party/cloud_tpu/inference_converter/...",
        "//third_party/py/cloud_ml_autoflow/...",
        "//third_party/py/envlogger/...",
        "//third_party/py/keras/...",
        "//third_party/yggdrasil_decision_forests/...",
    ],
)