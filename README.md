# trabalho Web
@Entity
public class Usuario {
       
      @Id
      @Column(name="id", nullable=false, unique=true)
      private int id;
       
      @Column(name="userName", nullable=false, unique=true)
      private String nomeUsuario;
       
      @Column(name="password", nullable=false, unique=false)
      private String senha;
  
      @Column(name="lastAccess", unique=true)
      @Temporal(TemporalType.DATE)
      private Date ultimoAcesso;
       
      public String getNomeUsuario() {
            return nomeUsuario;
      }
       
      public void setNomeUsuario(String nomeUsuario) {
            this.nomeUsuario = nomeUsuario;
      }
       
      public String getSenha() {
            return senha;
      }
       
      public void setSenha(String senha) {
            this.senha = senha;
      }
       
      public Date getUltimoAcesso() {
            return ultimoAcesso;
      }
       
      public void setUltimoAcesso(Date ultimoAcesso) {
            this.ultimoAcesso = ultimoAcesso;
      }
 }

  
import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.NoResultException;
import javax.persistence.Persistence;
  
import com.devmedia.model.Usuario;
  
public class UsuarioDAO {
  
    private EntityManagerFactory factory = Persistence
                .createEntityManagerFactory("usuarios");
    private EntityManager em = factory.createEntityManager();
 
    public Usuario getUsuario(String nomeUsuario, String senha) {
 
      try {
        Usuario usuario = (Usuario) em
         .createQuery(
             "SELECT u from Usuario u where u.nomeUsuario = 
             :name and u.senha = :senha")
         .setParameter("name", nomeUsuario)
         .setParameter("senha", senha).getSingleResult();
 
        return usuario;
      } catch (NoResultException e) {
            return null;
      }
    }
 
  public boolean inserirUsuario(Usuario usuario) {
          try {
                em.persist(usuario);
                return true;
          } catch (Exception e) {
                e.printStackTrace();
                return false;
          }
    }
     
    public boolean deletarUsuario(Usuario usuario) {
          try {
                em.remove(usuario);
                return true;
          } catch (Exception e) {
                e.printStackTrace();
                return false;
          }
    }
  
}

  
import javax.faces.application.FacesMessage;
import javax.faces.bean.ManagedBean;
import javax.faces.bean.ViewScoped;
import javax.faces.context.FacesContext;
  
import com.devmedia.db.UsuarioDAO;
import com.devmedia.model.Usuario;
  
@ManagedBean(name = "LoginMB")
@ViewScoped
public class LoginManagedBean {
  
  private UsuarioDAO usuarioDAO = new UsuarioDAO();
  private Usuario usuario = new Usuario();
   
  public String envia() {
         
    usuario = usuarioDAO.getUsuario(usuario.getNomeUsuario(), usuario.getSenha());
    if (usuario == null) {
      usuario = new Usuario();
      FacesContext.getCurrentInstance().addMessage(
         null,
         new FacesMessage(FacesMessage.SEVERITY_ERROR, "Usuário não encontrado!",
           "Erro no Login!"));
      return null;
    } else {
          return "/main";
    }
         
         
  }
 
  public Usuario getUsuario() {
    return usuario;
  }
 
  public void setUsuario(Usuario usuario) {
    this.usuario = usuario;
  }
}

<html xmlns="http://www.w3.org/1999/xhtml"
  xmlns:h="http://java.sun.com/jsf/html"
  xmlns:f="http://java.sun.com/jsf/core"
  xmlns:p="http://primefaces.org/ui">
  
<h:head>
  
</h:head>
  
<h:body>
  <h:form>
    <p:messages id="messages" />
    <p:panelGrid columns="2">
      <p:outputLabel for="nomeUsuario" value="Nome 
      Usuário:" />
      <p:inputText id="nomeUsuario"
      value="#{LoginMB.usuario.nomeUsuario}" />
       
      <p:outputLabel for="senha" value="Senha:" />
      <p:password id="senha" value="#{LoginMB.usuario.senha}" />
       
      <p:commandButton value="Enviar"
      icon="ui-icon-star" action="#{LoginMB.envia}" ajax="false">
      </p:commandButton>
 
    </p:panelGrid>
  </h:form>
</h:body>
</html>
