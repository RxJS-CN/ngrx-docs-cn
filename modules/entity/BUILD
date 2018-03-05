load("//tools:defaults.bzl", "ts_library")

ts_library(
    name = "entity",
    srcs = glob([
        "*.ts",
        "src/**/*.ts",
    ]),
    module_name = "@ngrx/entity",
    visibility = ["//visibility:public"],
    deps = [
        "//modules/store",
    ],
)
