FROM python:3.9

ARG PSITURK_VERSION
ENV PSITURK_VERSION=${PSITURK_VERSION:-3.2.0} \
    PSITURK_GLOBAL_CONFIG_LOCATION=/psiturk/

RUN test ${PSITURK_VERSION} = "dev" \
    && pip install \
           git+https://github.com/NYUCCL/psiTurk.git@master \
           PyMySQL \
    || pip install --upgrade \
           psiturk==${PSITURK_VERSION} \
           pymysql \
           certifi \
           python-Levenshtein \
    && apt-get clean autoclean \
    && apt-get autoremove -y \
    && rm -rf /var/lib/{apt,dpkg,cache,log}/

WORKDIR /psiturk

EXPOSE 22362
