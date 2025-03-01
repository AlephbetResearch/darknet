##
# Build file for Darknet core library
##
set( gpu_source
    activation_kernels.cu
    avgpool_layer_kernels.cu
    blas_kernels.cu
    col2im_kernels.cu
    convolutional_kernels.cu
    crop_layer_kernels.cu
    deconvolutional_kernels.cu
    dropout_layer_kernels.cu
    im2col_kernels.cu
    maxpool_layer_kernels.cu
    network_kernels.cu
    )

set( source
    activation_layer.c
    activations.c
    avgpool_layer.c
    batchnorm_layer.c
    blas.c
    box.c
    col2im.c
    compare.c
    connected_layer.c
    convolutional_layer.c
    cost_layer.c
    cpu_gemm.c
    crnn_layer.c
    crop_layer.c
    cuda.c
    data.c
    deconvolutional_layer.c
    demo.c
    detection_layer.c
    dropout_layer.c
    gemm.c
    gru_layer.c
    im2col.c
    image.c
    layer.c
    list.c
    local_layer.c
    lstm_layer.c
    matrix.c
    maxpool_layer.c
    network.c
    normalization_layer.c
    option_list.c
    parser.c
    region_layer.c
    reorg_layer.c
    rnn_layer.c
    route_layer.c
    shortcut_layer.c
    softmax_layer.c
    tree.c
    utils.c
    )

set( headers
    activation_layer.h
    activations.h
    avgpool_layer.h
    batchnorm_layer.h
    blas.h
    box.h
    classifier.h
    col2im.h
    connected_layer.h
    convolutional_layer.h
    cost_layer.h
    crnn_layer.h
    crop_layer.h
    cuda.h
    data.h
    deconvolutional_layer.h
    demo.h
    detection_layer.h
    dropout_layer.h
    gemm.h
    gru_layer.h
    im2col.h
    image.h
    layer.h
    list.h
    local_layer.h
    lstm_layer.h
    matrix.h
    maxpool_layer.h
    network.h
    normalization_layer.h
    option_list.h
    parser.h
    region_layer.h
    reorg_layer.h
    rnn_layer.h
    route_layer.h
    shortcut_layer.h
    softmax_layer.h
    stb_image.h
    stb_image_write.h
    tree.h
    utils.h
    darknet.h
    )

if( WIN32 )
    set( source
        ${source}
        cpu_gemm.c
        getopt.c
        gettimeofday.c )

    set( headers
        ${headers}
        getopt.h
        gettimeofday.h )
endif()

if( DARKNET_USE_GPU )
    CUDA_COMPILE( CUDA_SRC ${gpu_source} )
    list( APPEND DARKNET_LINKED_LIBS
        ${CUDA_CUDA_LIBRARY}
        ${CUDA_LIBRARIES}
        ${CUDA_CUBLAS_LIBRARIES}
        ${CUDA_curand_LIBRARY} )
endif()

add_library( darknetLib SHARED
    ${headers}
    ${source}
    ${CUDA_SRC}
    )

target_link_libraries( darknetLib
    PRIVATE  ${CMAKE_THREAD_LIBS_INIT}
    ${DARKNET_LINKED_LIBS}
    ${openCV_LIBS}
    )

target_compile_features(darknetLib PUBLIC cxx_std_11)

target_include_directories(darknetLib PUBLIC
    ${openCV_INCLUDE_DIR})

set(DARKNET_LIBS darknetLib)
set(DARKNET_INCLUDE_DIR libs/darknet/src)

if(WINDOWS)
    set_target_properties(darknetLib PROPERTIES
        LINK_FLAGS /SUBSYSTEM:CONSOLE
        VERSION "${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_PATCH}"
        SOVERSION ${VERSION_MAJOR}
        CXX_VISIBILITY_PRESET default
        )
else()
    set_target_properties(darknetLib PROPERTIES
        VERSION "${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_PATCH}"
        SOVERSION ${VERSION_MAJOR}
        CXX_VISIBILITY_PRESET default
        )

endif()


install( TARGETS darknetLib
    ARCHIVE DESTINATION     lib
    LIBRARY DESTINATION     lib
    RUNTIME DESTINATION     bin
    COMPONENT               runtime
    )

