add_definitions(-include ${CMAKE_CURRENT_SOURCE_DIR}/namespace-helper.hh)

if(HEFUR_CONTROL_INTERFACE)
  mimosa_protoc(hefur)

  add_library(hefur-proto hefur.pb.cc)

  add_executable(hefurctl
    hefurctl.cc)

  target_link_libraries(hefurctl
    hefur-proto
    mimosa-bittorrent
    mimosa-rpc
    mimosa
    ${GNUTLS_LIBRARY}
    pthread
    rt)

  install(TARGETS hefurctl
    RUNTIME DESTINATION bin
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib)

  set(HEFURD_CONTROL_SOURCES
    control-server.cc
    control-service.cc)

endif(HEFUR_CONTROL_INTERFACE)

add_executable(hefurd
  address.cc
  announce-handler.cc
  announce-request.cc
  file-handler.cc
  fs-tree-white-list.cc
  hefur.cc
  http-server.cc
  log.cc
  main.cc
  options.cc
  peer.cc
  peers-handler.cc
  scrape-handler.cc
  stat-handler.cc
  template-factory.cc
  torrent.cc
  torrent-db.cc
  udp-server.cc

  ${HEFURD_CONTROL_SOURCES})

if(HEFUR_CONTROL_INTERFACE)
  target_link_libraries(hefurd hefur-proto mimosa-rpc)
endif()

target_link_libraries(hefurd
  mimosa-tpl
  mimosa-http
  mimosa-fs
  mimosa-bittorrent
  mimosa-bencode
  mimosa
  ${GNUTLS_LIBRARY}
  pthread
  rt)

install(TARGETS hefurd
  RUNTIME DESTINATION bin
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  PUBLIC_HEADER DESTINATION include/hefur)

install(FILES tracker-controller.hh
  DESTINATION include/hefur)
