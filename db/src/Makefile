CC = ccache clang
CXX = ccache clang++

CFLAGS = -m32 -g -Wall -O2 -pipe -pthread -D_THREAD_SAFE
CXXFLAGS = -m32 -g -Wall -O2 -pipe -pthread -D_THREAD_SAFE \
	-fno-rtti -fno-exceptions -std=c++17 -Wno-invalid-source-encoding

BSD_VERSION = $(shell uname -v 2>&1 | cut -d' ' -f2 | cut -d'.' -f1)
PLATFORM = $(shell file /bin/ls | cut -d' ' -f3 | cut -d'-' -f1)

BINDIR = ..
BIN = $(BINDIR)/db

INCDIR =
LIBDIR =
LIBS =

OBJDIR = OBJDIR

$(shell if [ ! -d $(OBJDIR) ]; then mkdir $(OBJDIR); fi)

# External
INCDIR += -I../../../External/include
LIBDIR += -L../../../External/library

# MySQL
ifeq ($(BSD_VERSION), 14)
INCDIR += -I../../libmysql/8.0.33/14.0
LIBDIR += -L../../libmysql/8.0.33/14.0
else ifeq ($(BSD_VERSION), 13)
INCDIR += -I../../libmysql/8.0.33/13.0
LIBDIR += -L../../libmysql/8.0.33/13.0
else
INCDIR += -I../../libmysql/8.0.33
LIBDIR += -L../../libmysql/8.0.33
endif
LIBS += -lmysqlclient -lz -lzstd

# OpenSSL, CryptoPP
LIBS += -lssl -lcrypto

# Project Library
LIBDIR += -L../../libthecore/lib
LIBS += -lthecore
LIBDIR += -L../../libsql
LIBS += -lsql
LIBDIR += -L../../libpoly
LIBS += -lpoly
LIBDIR += -L../../libgame/lib
LIBS += -lgame

TARGET = $(BIN)

CPPFILE = Config.cpp NetBase.cpp Peer.cpp PeerBase.cpp Main.cpp Lock.cpp DBManager.cpp \
	Cache.cpp LoginData.cpp ClientManager.cpp ClientManagerPlayer.cpp ClientManagerLogin.cpp \
	ClientManagerBoot.cpp ClientManagerParty.cpp ClientManagerGuild.cpp GuildManager.cpp HB.cpp \
	PrivManager.cpp MoneyLog.cpp ItemAwardManager.cpp ClientManagerEventFlag.cpp Marriage.cpp \
	Monarch.cpp BlockCountry.cpp ItemIDRangeManager.cpp ClientManagerHorseName.cpp \
	ProtoReader.cpp CsvReader.cpp

OBJS = $(CPPFILE:%.cpp=$(OBJDIR)/%.o)

default: $(TARGET)

$(TARGET): $(OBJS)
	@echo "Linking $(TARGET)"
	@$(CXX) $(CXXFLAGS) $(LIBDIR) $(OBJS) $(LIBS) -o $(TARGET)

$(OBJDIR)/%.o: %.cpp
	@echo "Compiling C++ $<"
	@$(CXX) $(CXXFLAGS) $(INCDIR) -c $< -o $@

$(OBJDIR):
	@mkdir $(OBJDIR)

clean:
	@rm -f $(OBJS) $(BINDIR)/db_r*

dep:
	@touch Depend
	makedepend -f Depend $(INCDIR) -I/usr/include/c++/3.3 -I/usr/include/c++/4.2 -p$(OBJDIR)/ $(CPPFILE) 2> /dev/null

sinclude Depend