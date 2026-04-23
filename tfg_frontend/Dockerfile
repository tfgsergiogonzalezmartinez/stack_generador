
FROM node:latest as angular

ARG PUBLIC_URL
ENV ENV_PUBLIC_URL=${PUBLIC_URL}

WORKDIR /home/app
COPY package*.json .
RUN npm ci
COPY . .
RUN if [ -f Enviroment.ts ]; then rm Enviroment.ts; fi
RUN echo "Archivo Environment.ts eliminado (si existía)"

RUN WS_URL=$(echo "${ENV_PUBLIC_URL}" | sed 's|^https://|wss://|; s|^http://|ws://|'); \
    printf "export const Enviroment = {\n  BACKEND_URL : '%s',\n  BACKEND_API_URL : '%s/api/',\n  CHAT_WS : '%s/WebChat'\n};\n" \
    "${ENV_PUBLIC_URL}" "${ENV_PUBLIC_URL}" "${WS_URL}" > Enviroment.ts

RUN echo "Archivo environment.ts creado con variables de entorno"
RUN chmod 777 Enviroment.ts

RUN npm run build -- --configuration=production

FROM nginx:alpine
WORKDIR /usr/share/nginx/html
COPY --from=angular /home/app/dist/frontend_tfg/browser .
COPY ./nginx.conf /etc/nginx/conf.d/default.conf
RUN chmod -R +rx *
EXPOSE 80
# CMD [ "tail", "-f", "/dev/null" ]
