FROM quay.io/jupyter/pytorch-notebook:cuda12-python-3.11

ARG JUPYTERHUB_VERSION=5.4.4

COPY ./requirements.txt .

RUN pip3 install --no-cache -r requirements.txt

RUN pip3 install --no-cache \
    jupyterhub==$JUPYTERHUB_VERSION

RUN pip3 install --no-cache \
    nbgitpuller

# ─────────────────────────────────────────────────────────────────────────────
# Case Study 01 (DriveLM / EM-VLM4AD) — ENVIRONMENT ONLY.
# Datasets and fine-tuned checkpoints are NOT baked here; they live on the shared
# read-only volume mounted at /opt/shared and are staged by a setup cell in each
# notebook. Only fixed foundation-model backbones are precached below.
# ─────────────────────────────────────────────────────────────────────────────

# pycocoevalcap's METEOR scorer shells out to Java → install a headless JRE.
USER root
RUN apt-get update && apt-get install -y --no-install-recommends default-jre-headless \
 && rm -rf /var/lib/apt/lists/* \
 && mkdir -p /opt/hf-cache /opt/torch-cache \
 && chown -R ${NB_UID}:${NB_GID} /opt/hf-cache /opt/torch-cache
USER ${NB_UID}

# Disable only the SPICE scorer (needs an extra Stanford CoreNLP download). It is
# the last entry of the scorer list, so commenting it keeps the preceding lines valid.
RUN sed -i 's|^\(\s*\)(Spice(), "SPICE")|\1# (Spice(), "SPICE")|' \
      /opt/conda/lib/python3.11/site-packages/pycocoevalcap/eval.py

# Precache the fixed foundation backbones the notebooks pull at runtime so students
# don't each download ~1.2 GB mid-class (t5-base ~850 MB from HF, vit_b_32 ~337 MB
# from pytorch.org). Caches live under /opt so a home-volume mount can't shadow them.
ENV HF_HOME=/opt/hf-cache TORCH_HOME=/opt/torch-cache
RUN python -c "from transformers import T5ForConditionalGeneration, T5Tokenizer; T5ForConditionalGeneration.from_pretrained('google-t5/t5-base'); T5Tokenizer.from_pretrained('google-t5/t5-base')" \
 && python -c "from torchvision.models import vit_b_32, ViT_B_32_Weights; vit_b_32(weights=ViT_B_32_Weights.DEFAULT)"
