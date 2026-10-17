# HATIM5G
تطبيق فحص اشاره الراوتر
cd /workspaces/HATIM5G && unzip -o HatimApp-5.zip -d extracted && rm -rf app build.gradle.kts settings.gradle.kts gradle.properties gradle README.md SECURITY.md .github 2>/dev/null; mv extracted/HatimApp/* . && mv extracted/HatimApp/.github . 2>/dev/null; rm -rf extracted HatimApp-5.zip && git add -A && git commit -m "unpack project" && git push
