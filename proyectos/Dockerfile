# Para construir la imagen del contenedor custom:
# docker build -t curso-roles:latest .

FROM ghcr.io/ansible/creator-ee:v0.22.0

RUN ansible-galaxy collection install community.crypto -p /usr/share/ansible/collections/ansible_collections

RUN mkdir /roles

COPY roles/ /roles

ENV ANSIBLE_ROLES_PATH=/roles