################################################################################
# SPDX-FileCopyrightText: Copyright (c) 2022-2023 NVIDIA CORPORATION & AFFILIATES. All rights reserved.
# SPDX-License-Identifier: MIT
#
# Permission is hereby granted, free of charge, to any person obtaining a
# copy of this software and associated documentation files (the "Software"),
# to deal in the Software without restriction, including without limitation
# the rights to use, copy, modify, merge, publish, distribute, sublicense,
# and/or sell copies of the Software, and to permit persons to whom the
# Software is furnished to do so, subject to the following conditions:
#
# The above copyright notice and this permission notice shall be included in
# all copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL
# THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
# FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER
# DEALINGS IN THE SOFTWARE.
################################################################################

CUDA_VER?=
ifeq ($(CUDA_VER),)
  $(error "CUDA_VER is not set")
endif

APP:= deepstream-server-app

CC:= g++

TARGET_DEVICE = $(shell g++ -dumpmachine | cut -f1 -d -)

NVDS_VERSION:=6.4

LIB_INSTALL_DIR?=/opt/nvidia/deepstream/deepstream-$(NVDS_VERSION)/lib/
APP_INSTALL_DIR?=/opt/nvidia/deepstream/deepstream-$(NVDS_VERSION)/bin/

SRCS:= $(wildcard *.cpp)

INCS:= $(wildcard *.h) \

PKGS:= gstreamer-1.0

OBJS:= $(SRCS:.cpp=.o)

CFLAGS+= -I../../../includes \
	 -I../../apps-common/includes \
         -I /usr/local/cuda-$(CUDA_VER)/include \
	 -I/usr/include/jsoncpp

CFLAGS+= $(shell pkg-config --cflags $(PKGS))

LIBS:= $(shell pkg-config --libs $(PKGS))

LIBS+= -L/usr/local/cuda-$(CUDA_VER)/lib64/ -lcudart -lnvdsgst_helper -lnvdsgst_customhelper -lnvds_rest_server -lm \
		-L$(LIB_INSTALL_DIR) -lnvdsgst_meta -lnvds_meta -lnvds_yml_parser \
		-lcuda -Wl,-rpath,$(LIB_INSTALL_DIR) -lnvds_rest_server

ifeq ($(TARGET_DEVICE),aarch64)
   LIBS+= -L/usr/lib/aarch64-linux-gnu/tegra/ -lgstnvcustomhelper -ljsoncpp
else
   LIBS+= -L$(LIB_INSTALL_DIR) -lgstnvcustomhelper -ljsoncpp
endif

all: $(APP)

%.o: %.cpp $(INCS) Makefile
	$(CC) -c -o $@ $(CFLAGS) $<

$(APP): $(OBJS) Makefile
	$(CC) -o $(APP) $(OBJS) $(LIBS)

install: $(APP)
	cp -rv $(APP) $(APP_INSTALL_DIR)

clean:
	rm -rf $(OBJS) $(APP)


