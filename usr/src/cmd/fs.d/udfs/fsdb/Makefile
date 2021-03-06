#
# CDDL HEADER START
#
# The contents of this file are subject to the terms of the
# Common Development and Distribution License (the "License").
# You may not use this file except in compliance with the License.
#
# You can obtain a copy of the license at usr/src/OPENSOLARIS.LICENSE
# or http://www.opensolaris.org/os/licensing.
# See the License for the specific language governing permissions
# and limitations under the License.
#
# When distributing Covered Code, include this CDDL HEADER in each
# file and include the License file at usr/src/OPENSOLARIS.LICENSE.
# If applicable, add the following below this CDDL HEADER, with the
# fields enclosed by brackets "[]" replaced with your own identifying
# information: Portions Copyright [yyyy] [name of copyright owner]
#
# CDDL HEADER END
#
#
#pragma ident	"%Z%%M%	%I%	%E% SMI"
#
# Copyright 2007 Sun Microsystems, Inc.  All rights reserved.
# Use is subject to license terms.
#

FSTYPE=		udfs
LIBPROG=	fsdb

include		../../Makefile.fstype

# fsdb has a name clash with main() and libl.so.1.  However, fsdb must
# still export a number of "yy*" (libl) interfaces.  Reduce all other symbols
# to local scope.
MAPFILES +=	$(MAPFILE.INT) $(MAPFILE.LEX) $(MAPFILE.NGB)
MAPOPTS =	$(MAPFILES:%=-M%)

CPPFLAGS +=	-D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64
LDLIBS +=	-lmalloc -ll -ladm
LDFLAGS +=	$(MAPOPTS)
YFLAGS =	"-d"

LINTFLAGS +=	-erroff=E_STATIC_UNUSED
LINTFLAGS64 +=	-erroff=E_STATIC_UNUSED

SRCS=	fsdb.c ud_lib.c

fsdb : fsdb_yacc.o fsdb_lex.o ud_lib.o fsdb.o $(MAPFILES)
	$(LINK.c) -o $@ fsdb.o fsdb_yacc.o fsdb_lex.o \
			ud_lib.o $(LDLIBS)
	$(POST_PROCESS)

fsdb.o : fsdb.c
	$(COMPILE.c) -o $@ fsdb.c
	$(POST_PROCESS_O)

ud_lib.o : ../fstyp/ud_lib.c
	$(RM) ud_lib.c
	$(CP) ../fstyp/ud_lib.c ud_lib.c
	$(RM) ud_lib.h
	$(CP) ../fstyp/ud_lib.h ud_lib.h
	$(COMPILE.c) -o $@ ud_lib.c
	$(POST_PROCESS_O)

y.tab.c : fsdb_yacc.y
	$(YACC.y) fsdb_yacc.y

fsdb_yacc.o : y.tab.c
	$(COMPILE.c) -o $@ y.tab.c
	$(POST_PROCESS_O)

lex.yy.c : fsdb_lex.l
	$(LEX) -e fsdb_lex.l

fsdb_lex.o : lex.yy.c
	$(COMPILE.c) -o $@ lex.yy.c
	$(POST_PROCESS_O)

clean :
	$(RM) ud_lib.c ud_lib.h fsdb.o ud_lib.o fsdb_yacc.o fsdb_lex.o
	$(RM) fsdb_yacc.c fsdb_lex.c y.tab.c y.tab.h lex.yy.c

# for messaging catalog
#
POFILE= fsdb.po

# for messaging catalog
#
catalog:        $(POFILE)

CATSRCS=	$(SRCS) lex.yy.c y.tab.c

$(POFILE):      $(CATSRCS)
	$(RM) $@
	$(COMPILE.cpp) $(CATSRCS)   > $(POFILE).i
	$(XGETTEXT) $(XGETFLAGS)        $(POFILE).i
	$(SED) "/^domain/d" messages.po >  $@
	$(RM) $(POFILE).i messages.po

lint: y.tab.c lex.yy.c
	$(RM) ud_lib.h
	$(CP) ../fstyp/ud_lib.h ud_lib.h
	$(RM) ud_lib.c
	$(CP) ../fstyp/ud_lib.c ud_lib.c
	$(LINT.c) -mu $(SRCS)

putback :
	-cstyle -p fsdb.c
	-keywords fsdb.c
	-keywords Makefile

