FROM quay.io/water-hole/ansible-operator

COPY requirements.txt ${HOME}/requirements.txt
CMD pip install -r requirements.txt
COPY roles/ ${HOME}/roles/
COPY playbook.yaml ${HOME}/playbook.yaml
COPY watches.yaml ${HOME}/watches.yaml
