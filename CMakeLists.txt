if(CMAKE_SIZEOF_VOID_P EQUAL 4)
    set(WEBVIEW2_ARCH x86 CACHE STRING "WebView2 Architecture")
else()
    set(WEBVIEW2_ARCH x64 CACHE STRING "WebView2 Architecture")
endif()

set(
    WEBVIEW2_VERSION
    "1.0.1343.22"
    CACHE
    STRING
    "WebView2 Version"
)

set(
    WEBVIEW2_URL
    "https://api.nuget.org/v3-flatcontainer/microsoft.web.webview2/${WEBVIEW2_VERSION}/microsoft.web.webview2.${WEBVIEW2_VERSION}.nupkg"
)

set(
    WEBVIEW2_PACKAGE_DIR
    "${CMAKE_CURRENT_BINARY_DIR}/packages/Microsoft.Web.WebView2.${WEBVIEW2_VERSION}"
    CACHE
    PATH
    "WebView2 SDK Path"
)

if (NOT EXISTS WEBVIEW2_PACKAGE_DIR)
    file(
        DOWNLOAD
        ${WEBVIEW2_URL}
        ${CMAKE_CURRENT_BINARY_DIR}/webview2.nupkg
    )
    file(
        MAKE_DIRECTORY
        ${WEBVIEW2_PACKAGE_DIR}
    )
    execute_process(
        COMMAND
        "${CMAKE_COMMAND}" -E tar x "${CMAKE_CURRENT_BINARY_DIR}/webview2.nupkg"
        WORKING_DIRECTORY
        "${WEBVIEW2_PACKAGE_DIR}"
    )
endif()

add_library(webview2 INTERFACE)
add_library(webview2_static INTERFACE)

target_include_directories(webview2 INTERFACE "${WEBVIEW2_PACKAGE_DIR}/build/native/include")
target_include_directories(webview2_static INTERFACE "${WEBVIEW2_PACKAGE_DIR}/build/native/include")

target_link_libraries(
    webview2
    INTERFACE
    ${WEBVIEW2_PACKAGE_DIR}/build/native/${WEBVIEW2_ARCH}/WebView2Loader.dll.lib
)

target_link_libraries(
    webview2_static
    INTERFACE
    ${WEBVIEW2_PACKAGE_DIR}/build/native/${WEBVIEW2_ARCH}/WebView2LoaderStatic.lib
)
