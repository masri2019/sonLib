rootPath = ..

include ../include.mk

libSources = impl/*.c
cppSources = impl/*.cpp
libHeaders = $(wildcard inc/*.h)
libInternalHeaders = impl/*.h
libTests = tests/sonLib*.c tests/st*
quickTreeObjects = ../externalTools/quicktree_1.1/obj/buildtree.o ../externalTools/quicktree_1.1/obj/cluster.o ../externalTools/quicktree_1.1/obj/distancemat.o ../externalTools/quicktree_1.1/obj/options.o ../externalTools/quicktree_1.1/obj/sequence.o ../externalTools/quicktree_1.1/obj/tree.o ../externalTools/quicktree_1.1/obj/util.o
quickTreeLibPath = ../externalTools/quicktree_1.1/include/

testProgs = ${BINDIR}/sonLibTests ${BINDIR}/sonLib_kvDatabaseTest ${BINDIR}/sonLib_cigarTest ${BINDIR}/sonLib_fastaCTest

dbInclFlags = ${tokyoCabinetIncl} ${kyotoTycoonIncl} ${tokyoTyrantIncl} ${mysqlIncl} ${pgsqlIncl} -I${quickTreeLibPath} ${hiRedisIncl}

CPPFLAGS += ${dbInclFlags} -I ../externalTools/cutest
LDLIBS = ${LIBDIR}/sonLib.a ${dblibs} ${LIBS}

all : ${LIBDIR}/sonLib.a ${testProgs}

clean : 
	rm -rf  ${BINDIR} ${LIBDIR} *.o

${BINDIR}/sonLibTests : ${libTests} ${libInternalHeaders} ${LIBDIR}/sonLib.a ${LIBDIR}/cuTest.a tests/allTests.c
	@mkdir -p $(dir $@)
	${CC} ${CPPFLAGS} ${CFLAGS} ${LDFLAGS} -o $@ tests/allTests.c ${libTests} ${LIBDIR}/sonLib.a ${LIBDIR}/cuTest.a ${dblibs} ${mysqlLibs} ${LDLIBS} -lm -lstdc++ -lpthread

${BINDIR}/sonLib_kvDatabaseTest : ${libTests} ${libInternalHeaders} ${LIBDIR}/sonLib.a ${LIBDIR}/cuTest.a tests/kvDatabaseTest.c tests/kvDatabaseTestCommon.c
	@mkdir -p $(dir $@)
	${CC} ${CPPFLAGS} ${CFLAGS} ${LDFLAGS} -I tests -o $@ tests/kvDatabaseTest.c tests/kvDatabaseTestCommon.c ${LIBDIR}/sonLib.a ${LIBDIR}/cuTest.a ${dblibs} ${mysqlLibs} ${LDLIBS} -lm

${BINDIR}/sonLib_cigarTest : tests/cigarsTest.c ${libTests} ${libInternalHeaders} ${LIBDIR}/sonLib.a 
	@mkdir -p $(dir $@)
	${CC} ${CPPFLAGS} ${CFLAGS} ${LDFLAGS} -o $@ tests/cigarsTest.c ${LDLIBS}

${BINDIR}/sonLib_fastaCTest : tests/fastaCTest.c ${libTests} ${libInternalHeaders} ${LIBDIR}/sonLib.a
	@mkdir -p $(dir $@)
	${CC} ${CPPFLAGS} ${CFLAGS} ${LDFLAGS} -o $@ tests/fastaCTest.c ${LDLIBS}

${BINDIR}/kt_connect_test : tests/kt_connect_test.cpp ${libTests} ${libInternalHeaders} ${LIBDIR}/sonLib.a
	@mkdir -p $(dir $@)
	${CXX} ${CXXFLAGS} ${CPPFLAGS} -o $@ tests/kt_connect_test.cpp ${LIBDIR}/sonLib.a ${dblibs} ${LDLIBS} -lm

${LIBDIR}/%.h: inc/%.h
	@mkdir -p $(dir $@)
	cp $< $@

${LIBDIR}/sonLib.a : ${libSources} ${cppSources} $(foreach l, ${libHeaders}, ${LIBDIR}/$(notdir $l)) ${libInternalHeaders}
	@mkdir -p $(dir $@)
	${CC} ${CPPFLAGS} ${CFLAGS} -c ${libSources}
	${CXX} ${CXXFLAGS} ${CPPFLAGS} -c ${cppSources}
	${AR} rc sonLib.a *.o ${quickTreeObjects}
	${RANLIB} sonLib.a 
	mv sonLib.a ${LIBDIR}/

test:
	${PYTHON} allTests.py --testLength=SHORT --logLevel CRITICAL
