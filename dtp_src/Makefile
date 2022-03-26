CC = clang
CFLAGS += -std=c11 -Wall
SRCS = dtp.c
OBJS = $(SRCS:.c=.o)
MAIN = dtp

.PHONY: all clean

all: $(MAIN)

clean:
	$(RM) *.o $(DESTDIR)$(MAIN)

$(MAIN): $(OBJS)
	$(CC) $(CFLAGS) $(OBJS) -s -o $(MAIN)

.c.o:
	$(CC) $(CFLAGS) -c $< -o $@