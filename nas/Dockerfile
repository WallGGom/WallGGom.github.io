FROM tusproject/tusd:v2.10.1
USER root
COPY hooks/ /srv/tusd-hooks/
# DSM 에서 복사하면 실행 비트가 빠지고, Windows 를 거치면 CRLF 가 붙는다
RUN sed -i 's/\r$//' /srv/tusd-hooks/* && chmod 755 /srv/tusd-hooks/*
