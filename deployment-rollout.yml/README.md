1. Do a rolling update of the nginx versions from `stable` to `1.21.5`
2. See rollout history: `k rollout history deploy nginx-deployment`
3. See rollout revisions: `k rollout history deploy nginx-deployment --revision=1`
4. Rollback: `k rollout undo deploy nginx-deployment --to-revision=1`
