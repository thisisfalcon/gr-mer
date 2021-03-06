# Copyright 2011,2012 Free Software Foundation, Inc.
#
# This file is part of GNU Radio
#
# GNU Radio is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 3, or (at your option)
# any later version.
#
# GNU Radio is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with GNU Radio; see the file COPYING.  If not, write to
# the Free Software Foundation, Inc., 51 Franklin Street,
# Boston, MA 02110-1301, USA.

########################################################################
# Setup library
########################################################################
include(GrPlatform) #define LIB_SUFFIX

include_directories(${Boost_INCLUDE_DIR})
link_directories(${Boost_LIBRARY_DIRS})

list(APPEND mer_sources
    probe_mer_c_impl.cc
    demapper.cc
    mer.cc
    probe_ste_cf_impl.cc
    ste.cc
    probe_cs_cf_impl.cc
    carrier_suppression.cc
    probe_ai_cf_impl.cc
    amplitude_imbalance.cc
    probe_qe_cf_impl.cc
    quadrature_error.cc
    probe_pj_cf_impl.cc
    phase_jitter.cc
    probe_all_meassurements_cf_impl.cc
)

set(mer_sources "${mer_sources}" PARENT_SCOPE)
if(NOT mer_sources)
	MESSAGE(STATUS "No C++ sources... skipping lib/")
	return()
endif(NOT mer_sources)

add_library(gnuradio-mer SHARED ${mer_sources})
target_link_libraries(gnuradio-mer ${Boost_LIBRARIES} ${GNURADIO_ALL_LIBRARIES})
set_target_properties(gnuradio-mer PROPERTIES DEFINE_SYMBOL "gnuradio_mer_EXPORTS")

if(APPLE)
    set_target_properties(gnuradio-mer PROPERTIES
        INSTALL_NAME_DIR "${CMAKE_INSTALL_PREFIX}/lib"
    )
endif(APPLE)

########################################################################
# Install built library files
########################################################################
install(TARGETS gnuradio-mer
    LIBRARY DESTINATION lib${LIB_SUFFIX} # .so/.dylib file
    ARCHIVE DESTINATION lib${LIB_SUFFIX} # .lib file
    RUNTIME DESTINATION bin              # .dll file
)

########################################################################
# Build and register unit test
########################################################################
include(GrTest)

include_directories(${CPPUNIT_INCLUDE_DIRS})

list(APPEND test_mer_sources
    ${CMAKE_CURRENT_SOURCE_DIR}/test_mer.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/qa_mer.cc
)

add_executable(test-mer ${test_mer_sources})

target_link_libraries(
  test-mer
  ${GNURADIO_RUNTIME_LIBRARIES}
  ${Boost_LIBRARIES}
  ${CPPUNIT_LIBRARIES}
  gnuradio-mer
)

GR_ADD_TEST(test_mer test-mer)
