FROM python:3.13.18-alpine3.24

WORKDIR /app

# Install dependencies first so this layer is cached between builds
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt \
    && pip uninstall -y pip setuptools  # not needed at runtime; pip bundles vulnerable urllib3/msgpack

COPY app.py .

# Run as an unprivileged user instead of root
RUN addgroup -S festpass && adduser -S -G festpass festpass
USER festpass

ARG APP_VERSION=dev
ENV APP_VERSION=${APP_VERSION}

EXPOSE 5000

CMD ["python", "app.py"]
