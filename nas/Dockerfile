FROM tusproject/tusd:v2.10.1@sha256:7b1c552a8b42f4b36cb01f2a3bd49f82ab078b2eefd191459e716141dd50376c
USER root
COPY hooks/ /srv/tusd-hooks/
# DSM 에서 복사하면 실행 비트가 빠지고, Windows 를 거치면 CRLF 가 붙는다
RUN sed -i 's/\r$//' /srv/tusd-hooks/* && chmod 755 /srv/tusd-hooks/*
