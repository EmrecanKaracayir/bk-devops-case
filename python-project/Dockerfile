# >-----< BASE IMAGE >-----< #

FROM python:3.13-slim AS base

ENV \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    \
    PIP_NO_INPUT=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    \
    PATH="/app/.venv/bin:$PATH"

RUN \
    addgroup --system appgroup && \
    adduser --system --no-create-home --ingroup appgroup appuser

# >-----< INSTALL STAGE >-----< #

FROM base AS installer

WORKDIR /app/

COPY requirements.txt ./

RUN \
    python -m venv .venv && \
    .venv/bin/pip install -r requirements.txt && \
    rm requirements.txt

# >-----< BUILD STAGE >-----< #

FROM installer AS builder

COPY ETL.py ./

# >-----< TEST STAGE >-----< #

FROM builder AS tester

RUN echo "[🔵]: No tests defined."

# >-----< RUN STAGE >-----< #

FROM base AS runner

WORKDIR /app/

COPY --from=builder --chown=appuser:appgroup /app/ ./

USER appuser

CMD ["python", "ETL.py"]
