cmake_minimum_required(VERSION 3.25 FATAL_ERROR)
project(BeShare LANGUAGES CXX C)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# ---------------------------------------------------------------------------
# MUSCLE networking library (vendored as a git submodule under source/muscle)
# ---------------------------------------------------------------------------
set(MUSCLE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/source/muscle)

set(MUSCLE_SOURCES
    ${MUSCLE_DIR}/besupport/ConvertMessages.cpp
    ${MUSCLE_DIR}/iogateway/AbstractMessageIOGateway.cpp
    ${MUSCLE_DIR}/iogateway/MessageIOGateway.cpp
    ${MUSCLE_DIR}/iogateway/PlainTextMessageIOGateway.cpp
    ${MUSCLE_DIR}/message/Message.cpp
    ${MUSCLE_DIR}/reflector/AbstractReflectSession.cpp
    ${MUSCLE_DIR}/reflector/DataNode.cpp
    ${MUSCLE_DIR}/reflector/DumbReflectSession.cpp
    ${MUSCLE_DIR}/reflector/RateLimitSessionIOPolicy.cpp
    ${MUSCLE_DIR}/reflector/ReflectServer.cpp
    ${MUSCLE_DIR}/reflector/ServerComponent.cpp
    ${MUSCLE_DIR}/reflector/SignalHandlerSession.cpp
    ${MUSCLE_DIR}/reflector/StorageReflectSession.cpp
    ${MUSCLE_DIR}/regex/PathMatcher.cpp
    ${MUSCLE_DIR}/regex/QueryFilter.cpp
    ${MUSCLE_DIR}/regex/StringMatcher.cpp
    ${MUSCLE_DIR}/syslog/SysLog.cpp
    ${MUSCLE_DIR}/system/AcceptSocketsThread.cpp
    ${MUSCLE_DIR}/system/MessageTransceiverThread.cpp
    ${MUSCLE_DIR}/system/SetupSystem.cpp
    ${MUSCLE_DIR}/system/SignalMultiplexer.cpp
    ${MUSCLE_DIR}/system/Thread.cpp
    ${MUSCLE_DIR}/util/ByteBuffer.cpp
    ${MUSCLE_DIR}/util/Directory.cpp
    ${MUSCLE_DIR}/util/FilePathInfo.cpp
    ${MUSCLE_DIR}/util/MiscUtilityFunctions.cpp
    ${MUSCLE_DIR}/util/NetworkUtilityFunctions.cpp
    ${MUSCLE_DIR}/util/PulseNode.cpp
    ${MUSCLE_DIR}/util/String.cpp
    ${MUSCLE_DIR}/util/SocketMultiplexer.cpp
    ${MUSCLE_DIR}/zlib/ZLibCodec.cpp
    ${MUSCLE_DIR}/zlib/ZLibUtilityFunctions.cpp
)

set(BESHARE_SOURCES
    source/ChatWindow.cpp
    source/ColorPicker.cpp
    source/main.cpp
    source/md5.cpp
    source/PrivateChatWindow.cpp
    source/ReflowingTextView.cpp
    source/RemoteFileItem.cpp
    source/RemoteUserItem.cpp
    source/ResultsView.cpp
    source/ShareApplication.cpp
    source/ShareColumn.cpp
    source/ShareFileTransfer.cpp
    source/ShareMIMEInfo.cpp
    source/ShareNetClient.cpp
    source/ShareStrings.cpp
    source/ShareUtils.cpp
    source/ShareWindow.cpp
    source/TransferListView.cpp
    source/UserListView.cpp
)

# ---------------------------------------------------------------------------
# When building inside the Vitruvian source tree, use the Application() macro
# which wires in libbe, libroot, resources, and the LinuxBuildCompatibility
# forced-include.  Outside the tree, fall back to a plain add_executable so
# the file can still be syntax-checked and incrementally developed.
# ---------------------------------------------------------------------------
if(COMMAND Application)
    Application(
        BeShare

        SOURCES
            ${BESHARE_SOURCES}
            ${MUSCLE_SOURCES}

        LIBS translation network z
        RDEF source/BeShare.rdef
    )
else()
    add_executable(BeShare ${BESHARE_SOURCES} ${MUSCLE_SOURCES})
    target_link_libraries(BeShare PRIVATE be translation network z stdc++)
endif()

target_include_directories(BeShare SYSTEM PRIVATE ${MUSCLE_DIR})
target_include_directories(BeShare PRIVATE source)

target_compile_definitions(BeShare PRIVATE
    _BSD_SOURCE
    MUSCLE_ENABLE_ZLIB_ENCODING
    MUSCLE_AVOID_IPV6
    MUSCLE_NO_EXCEPTIONS
    MUSCLE_USE_PTHREADS
)

target_compile_options(BeShare PRIVATE -O2 -Wall -Wextra)
